---
layout: single
title: Streamlit 설치하기
date: 2024-02-19 00:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, Python, Anaconda, 파이썬, Streamlit, 웹, Web]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

파이썬으로 웹을 만들 때, 플라스크(Flask), FastAPI와 같이 URL 에 따라 호출되는 HTML 형식의 페이지를 보여주는 가벼운 프레임워크나 장고(Django) 같은 풀스택 프레임워크를 주로 사용해 왔습니다.
하지만 최근에는 파이썬을 이용한 데이터 분석이 각광을 받으면서 데이터 과학자나 엔지니어가 쉽게 쓸 수 있는 Streamlit 라이브러리를 활용하고 있습니다. 

### Streamlit 설치하기

- 명령어 : `pip install streamlit`

![install_streamlit](/assets/images/2024-02-19-install-streamlit.png){: .align-center}
<p style="text-align: center;">Streamlit 설치</p>

설치를 마쳤다면 데모 페이지를 실행합니다. 

- 명령어 : `streamlit hello`

![execute_hello_streamlit](/assets/images/2024-02-19-execute-hello-streamlit.png){: .align-center}
<p style="text-align: center;">Streamlit Hello 데모 페이지 실행</p>

![execute_hello_demo_web](/assets/images/2024-02-19-execute-hello-demo-web.png){: .align-center}
<p style="text-align: center;">Streamlit Hello 데모 화면</p>


### 웹 페이지 만들기

웹 페이지 코드를 작성합니다. 

- 위치 : `web.py`

```python
# -*- coding:utf-8 -*-
import streamlit as st

st.title("제목을 표시합니다.")
user_input = st.text_input("사용자 입력을 받습니다.")
st.write(user_input)
if st.button("버튼입니다."):
    st.success("버튼이 눌렸습니다.")

```

웹 서버를 실행합니다.

- 명령어 : `streamlit run web.py`

![running_streamlit_test_web](/assets/images/2024-02-19-running-streamlit-test-web.png){: .align-center}
<p style="text-align: center;">Streamlit Hello 데모 화면</p>

### 사용법

다양한 기능을 제공하기에 일부 기능만을 설명하며, 자세한 내용은 참조에 첨부한 Official Docs 를 확인하기 바랍니다.

> **설정값**
- 제목 : `st.title("제목")`
- 문자 입력 : `input = st.text_input("문자 입력")`
- 숫자 입력 : `number = st.number_input("숫자 입력")`
- 쓰기 : `st.write("텍스트")`
- 여러줄 쓰기 : `st.text_area("첫줄", "둘째줄", "셋째줄")`
- 버튼 : `st.button("버튼이름")`
- 성공 메시지 출력 : `st.success("성공 메시지")`
- 경고 메시지 출력 : `st.warning("경고 메시지")`


### 참고

- Stream Official Docs : [https://docs.streamlit.io](https://docs.streamlit.io/)


