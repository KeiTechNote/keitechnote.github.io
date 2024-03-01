---
layout: single
title: VDR용 웹페이지 만들기
date: 2024-03-01 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, 블록체인, 이더리움, 스마트 컨트랙트, SSI, DID, API, API 서버, Python, 파이썬, Streamlit, 스트림릿]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

보통 콘솔을 기준으로 프로그램을 개발한 경우, 테스트를 위한 입력이나 실행 결과도 콘솔로 확인합니다. 
처음 사용은 쉽고 간편하지만, 반복적으로 테스트하는 경우 불편함을 느끼게 되고 자연스레 웹과 같은 보기 쉬운 환경을 고려하게 됩니다. 
따라서, API 서버와 상호작용하는 웹을 작성해 테스트 편의성을 개선해 보겠습니다. 

### 사전 프로그램 설치

API 서버와 같은 파이썬 환경에서 웹을 개발할 때 사용되는 `Streamlit` 라이브러리를 사용합니다.

- [Streamlit 설치하기](https://keitechnote.github.io/dev/dev-install-streamlit/)

### 기본 웹 페이지 만들기

등록(`register`)과 조회(`resolver`) 기능을 갖는 기본 웹 페이지를 만듭니다. 

- 위치 : ~\Dev\api\web.py

```python
# -*- coding:utf-8 -*-
from requests.models import Response

import streamlit as st
import requests

# API 서버 주소
api_url = "http://localhost:8000"

def call_register(_did:str, _document:str) -> Response:
    endpoint = f"{api_url}/register"
    params = {"_did": _did, "_document": _document}
    return requests.post(endpoint, params=params)

def call_resolver(_did:str) -> Response:
    endpoint = f"{api_url}/resolve/{_did}"
    return requests.get(endpoint)


def main():
    st.title("VDR Web Page (using Streamlit)")

    # 사용자에게 입력받기
    insert_did = st.text_input("Enter DID : ", key="insert_did")
    insert_document = st.text_input("Enter Document : ", key="insert_document")

    # Register 호출
    if st.button("Register"):
        if insert_did and insert_document:
            resp = call_register(insert_did, insert_document)
            if resp.status_code == 200:
                result = resp.json()
                st.success(f"Register Result : {result}")
            else:
                st.warning(f"[ERROR] {resp}")


    # 사용자에게 입력받기
    search_did = st.text_input("Enter DID : ", key="search_did")
    # Resolver 호출
    if st.button("Resolver"):
        if search_did:
            resp = call_resolver(search_did)
            if resp.status_code == 200:
                result = resp.json()
                st.success(f"Resolver Result : {result}")
            else:
                st.warning(f"[ERROR] {resp}")

if __name__ == "__main__":
    main()
```

웹 페이지를 실행합니다. 

- 명령어 : `streamlit run web.py`

> 작성한 웹페이지는 API 서버와, API 서버는 Ganache GUI와 상호작용하기 때문에 다음의 순서대로 실행해야 합니다. 
1. Ganache GUI 실행
2. VDR 스마트 컨트랙트 배포
3. API 서버 실행 (스마트 컨트랙트 주소 변경)
4. 웹페이지 실행
![execute_servers](/assets/images/2024-03-01-execute-servers.png){: .align-center}
{: .notice--info} 

### 웹페이지 탭 기능 추가하기

현재와 같은 방식으로 웹 페이지를 작성하면, 새로운 페이지를 만들 때마다 Streamlit 을 실행해야 합니다. 
따라서, 기능 또는 목적별로 나눌 수 있도록 웹페이지에 탭을 추가합니다. 
Streamlit 을 사용한 페이지를 구분하기 위해서 별도 라이브러리를 설치합니다. 

- 명령어 : `pip install st-pages`

탭을 넣은 웹페이지 구조를 만듭니다.

- 위치 : ~\Dev\api\web2.py

```python
# -*- coding:utf-8 -*-
from st_pages import Page, add_page_title, show_pages

show_pages(
    [
        Page("pages/home.py", "Home" , "🏠"),
        Page("pages/page_one.py", "VDR Test"),
        Page("pages/page_two.py", "Example Two"),
    ]
)
```

![tree_file](/assets/images/2024-03-01-tree-files.png){: .align-center}
<p style="text-align: center;">파일 구조</p>


생성할 페이지는 홈화면, web.py 의 기능을 하는 화면, 샘플 페이지로 총 3개입니다.
`web2.py` 를 제외한 각 페이지 파일을 쉽게 관리하기 위해 pages 디렉토리를 만든 후 작성합니다.
`web.py` 를 옮길 "VDR Test" 페이지를 제외한 나머지 페이지는 타이틀만 작성합니다. 

```python
# -*- coding:utf-8 -*-
# home.py
import streamlit as st

st.title("Testing Page")

# -*- coding:utf-8 -*-
# page_two.py
import streamlit as st

st.title("Page Two")
```

web.py 는 다음과 같이 코드를 조금 수정해 옮깁니다.

```python
# -*- coding:utf-8 -*-
from requests.models import Response

import streamlit as st
import requests


api_url = "http://localhost:8000"

def call_register(_did:str, _document:str) -> Response:
    endpoint = f"{api_url}/register"
    params = {"_did": _did, "_document": _document}
    return requests.post(endpoint, params=params)

def call_resolver(_did:str) -> Response:
    endpoint = f"{api_url}/resolve/{_did}"
    return requests.get(endpoint)

st.title("VDR Web Page (using Streamlit)")

# 사용자에게 입력받기
insert_did = st.text_input("Enter DID : ", key="insert_did")
insert_document = st.text_input("Enter Document : ", key="insert_document")

# Register 호출
if st.button("Register"):
    if insert_did and insert_document:
        resp = call_register(insert_did, insert_document)
        if resp.status_code == 200:
            result = resp.json()
            st.success(f"Register Result : {result}")
        else:
            st.warning(f"[ERROR] {resp}")


# 사용자에게 입력받기
search_did = st.text_input("Enter DID : ", key="search_did")
# Resolver 호출
if st.button("Resolver"):
    if search_did:
        resp = call_resolver(search_did)
        if resp.status_code == 200:
            result = resp.json()
            st.success(f"Resolver Result : {result}")
        else:
            st.warning(f"[ERROR] {resp}")
```

기존의 main() 함수가 사라지고 main() 코드의 들여쓰기만 제거했습니다. 
Streamlit 을 실행합니다.

- 명령어 : `streamlit run web2.py`

![test_pages_using_tab](/assets/images/2024-03-01-test-pages-using-tab.png){: .align-center}
<p style="text-align: center;">탭 반영한 테스트 페이지</p>

왼쪽 사이드에 탭이 생성되었고, 기존 web.py 에 작성한 코드도 정상 동작하는 것을 볼 수 있습니다. 

