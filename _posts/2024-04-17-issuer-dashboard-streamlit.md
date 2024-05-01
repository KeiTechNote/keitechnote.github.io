---
layout: single
title: Issuer 웹페이지 만들기
date: 2024-04-17 05:30 +09:00
published: true
categories: [issuer]
tag: [Dev, SSI, DID, Python, FastAPI, 파이썬, Streamlit, 스트림릿, DID Document, ECDSA, DID Syntax, 구문, DID 구문, method-name, method-specific-id, DID-Core]
toc: true
author_profile: false
---

### 개요

작성된 DID Document 를 VDR 에 저장해 누구든 접근할 수 있도록 합니다. 또한 이 과정을 보기 쉽도록 웹 페이지로 구성합니다. 

### 사전 프로그램 설치 

생성된 DID Document 를 저장하기 위해 VDR 을 사용합니다. 또한 VDR 통신을 위한 API 서버를 사용합니다. 

- [스마트 컨트랙트용 API 서버 만들기](https://keitechnote.github.io/vdr/vdr-api-server/)

API 서버와 같은 파이썬 환경에서 웹을 개발할 때 사용되는 `Streamlit` 라이브러리를 사용합니다.

- [Streamlit 설치하기](https://keitechnote.github.io/dev/dev-install-streamlit/)

> 작성된 Issuer 는 API 서버와, API 서버는 Ganache GUI 와 상호작용하기 때문에, 다음의 순서대로 실행해야 합니다. 
1. Ganache GUI 실행
2. VDR 스마트 컨트랙트 배포
3. API 서버 실행 (스마트 컨트랙트 주소 변경)
4. 웹페이지 실행
![execute_servers](/assets/images/2024-03-01-execute-servers.png){: .align-center}
{: .notice--info} 

### 공통 기능 만들기

SSI 생태계의 Issuer, Holder, Verifier 의 공통된 기능은 DID, DID Document 를 생성할 수 있다는 점입니다. 
따라서, [DID, DID Document 만들기](https://keitechnote.github.io/issuer/issuer-simple-issuer/) 를 참고해 Account 생성과 DID, DID Document 를 생성하는 공통 기능을 별도 모듈로 작성합니다.

공통 모듈명을 `kernel.py`로 지정하고, 파일은 모든 참가자들이 접근할 수 있는 위치에 생성합니다. 

- 파일 위치 : ~/api/pages/peers

#### Account 생성 API 만들기

이전 Post 의 내용을 기반으로 다음과 같이 Account 생성 함수를 작성합니다. 

```python
from ecdsa import SigningKey, VerifyingKey, SECP256k1

def _generate_keypair() -> tuple[SigningKey, VerifyingKey]:
    """
    ECDSA 키 쌍을 생성합니다. 
    """
    private_key = SigningKey.generate(curve=SECP256k1)
    public_key = private_key.verifying_key
    return private_key, public_key

def create_account() -> tuple:
    """
    _generate_keypair() 함수를 호출하는 Wrapper 함수입니다. 
    """
    return _generate_keypair()
```

#### DID 생성 API 만들기

DID 생성 함수를 작성합니다. 

```python
import base58

def _generate_did(method_name:str, public_key:str) -> str:
    """
    Public Key 로 DID 를 생성합니다. 
    """
    return f"did:{method_name}:{base58.b58encode(public_key.encode()).decode()}"

def create_did(method_name:str, public_key:str) -> str:
    """
    _generate_did() 함수를 호출하는 Wrapper 함수입니다. 
    """
    return _generate_did(method_name, public_key)
```

#### DID Document 생성 API 만들기

DID Document 생성 API 를 작성합니다. 

```python

def _generate_did_document(did:str, public_key:str) -> dict:
    """
    DID Document 를 생성합니다. 
    """
    did_document = {
        "@context": "https://www.w3.org/ns/did/v1",
        "id": did,
        "publicKey": [
            {
                "id": did + "#keys-1",
                "type": "EcdsaSecp256k1VerificationKey2019",
                "controller": did,
                "publicKeyHex": public_key
            }
        ],
        "authentication": [
            {
                "type": "Ed25519SignatureAuthentication2018",
                "publicKey": did + "#keys-1"
            }
        ]
    }
    return did_document

def create_did_doc(did:str, public_key:str) -> dict:
    """
    _generate_did_document() 함수를 호출하는 Wrapper 함수입니다.
    """
    return _generate_did_document(did, public_key)
```

### Issuer 기본 페이지 만들기 

작성된 공통 기능을 활용해 Issuer 페이지에서 Account 를 생성하고, DID, DID Document 를 작성하는 기능을 작성합니다. 

#### Issuer 를 탭으로 분리하기 

Issuer 는 VDR 과 분리된 별도 페이지로 생성합니다. 
이를 위해 pages 디렉토리에 빈 `issuer.py` 파일을 생성한 후 다음과 같이 탭으로 등록합니다. 

```python
# -*- coding:utf-8 -*-
from st_pages import Page, show_pages

show_pages(
    [
        Page("pages/home.py", "Home" , "🏠"),
        Page("pages/vdr.py", "VDR Test"),
        Page("pages/issuer.py", "Issuer"),
    ]
)
```

![empty_issuer_page](/assets/images/2024-04-17-empty-issuer-page.png){: .align-center}
<p style="text-align: center;">빈 Issuer 페이지 생성</p>

#### Isser 기본 페이지 만들기

작성한 kernel.py 를 사용하기 위해 Import 하고, Issuer 페이지임을 알 수 있도록 타이틀을 등록합니다. 

```python
from pages.peers import kernel
import streamlit as st


api_url = "http://localhost:8000"

st.title("Issuer (using Streamlit)")    
```

#### Account 생성하기

kernel 모듈의 create_account() 를 사용해 손쉽게 Account 를 생성할 수 있습니다. 

```python
st.subheader("Account 를 생성합니다. ")
if st.button("Create Account"):
    private_key, public_key = kernel.create_account()
    st.write("Public Key")
    st.success(f"{public_key.to_string().hex()}")
    st.write("Private Key")
    st.success(f"{private_key.to_string().hex()}")
```

![simple_create_account](/assets/images/2024-04-17-simple-create-account.png){: .align-center}
<p style="text-align: center;">Issuer Account 생성</p>


#### DID 생성하기

앞서 생성한 Account 의 Public Key 를 입력해 DID 를 생성합니다. 

```python
st.subheader("DID 를 생성합니다.")
insert_public_key = st.text_input("Enter Account's Public Key : ", key="insert_public_key")
if st.button("Generate DID"):
    did = kernel.create_did("dkei", insert_public_key)
    st.write("DID")
    st.success(f"{did}")
```

![simple_create_did](/assets/images/2024-04-17-simple-create-did.png){: .align-center}
<p style="text-align: center;">Issuer DID 생성</p>

#### DID Document 생성하기

Account 의 Public Key 와 DID 를 입력해 DID Document 를 생성합니다.

```python
st.subheader("DID Document 를 생성합니다.")
insert_did = st.text_input("Enter DID : ", key="insert_did")
if st.button("Generate DID Document"):
    did_doc = kernel.create_did_doc(insert_did, insert_public_key)
    st.write("DID Document")
    st.success(f"{did_doc}")
```
![simple_create_did_doc](/assets/images/2024-04-17-simple-create-did-doc.png){: .align-center}
<p style="text-align: center;">Issuer DID Document 생성</p>

### Issuer 페이지 개선하기 

API 호출하고 결과를 확인하는 기본적인 기능을 모두 작성되었습니다. 
하지만, 생성한 Public Key 나 DID 를 복사해야 하는 번거로운 부분과 같은 페이지이기에 변수를 재사용하거나 Scope 등의 신경쓰이는 부분이 남았습니다. 
이를 좀 더 개선하기 위해, Public Key, DID, DID Document 를 Streamlit 의 Stateful Button 을 활용해 언제든 접근할 수 있도록 수정합니다. 

#### Stateful button 활용하기 

Streamlit 에는 Session 의 State를 저장하고 언제든 활용할 수 있도록 `stateful button` 기능을 제공합니다. 
즉, `st.session_state`  변수에 임의로 `Attribute` 를 추가하고 값을 저장하는 방식입니다. 

Account 생성 코드를 살펴보면, 제일 먼저 `is_exist_account` 속성이 있는지 확인합니다. 
없을 경우, setattr 과 같은 Attribute 추가없이 `st.seesion_state.is_exist_account`로 바로 추가되고, 값을 지정할 수 있습니다.
같은 방법으로 앞으로 저정할 `account` 변수도 미리 초기화합니다. 

`st.button` 에 `on_click` 속성에 위에 정의한 `create_account` 함수명을 지정하면, `Create Account` 버튼을 누를 때, on_click 에 정의한 create_account 함수가 호출됩니다. 
create_account 함수는 Account 를 생성하고, 사전에 정의한 st.session_state.account 와 st.session_state.is_exist_account 변수에 각각 저장합니다. 

```python
#### Account
if "is_exist_account" not in st.session_state:
    st.session_state.is_exist_account = False
    st.session_state.account = {}

def create_account():
    private_key, public_key = kernel.create_account()
    st.session_state.account = {
        "public_key" : public_key,
        "private_key" : private_key
    }
    st.session_state.is_exist_account = True

st.subheader("Account 를 생성합니다. ")
st.button("Create Account", on_click=create_account)
if st.session_state.is_exist_account:
    account = st.session_state.account
    st.write("Public Key")
    st.success(f"{account.get('public_key').to_string().hex()}")
    st.write("Private Key")
    st.success(f"{account.get('private_key').to_string().hex()}")
```

전체 코드는 다음과 같습니다. 

```python
from pages.peers import kernel

import streamlit as st


api_url = "http://localhost:8000"

st.title("Issuer (using Streamlit)")    

#### Account
if "is_exist_account" not in st.session_state:
    st.session_state.is_exist_account = False
    st.session_state.account = {}

def create_account():
    private_key, public_key = kernel.create_account()
    st.session_state.account = {
        "public_key" : public_key,
        "private_key" : private_key
    }
    st.session_state.is_exist_account = True

st.subheader("Account 를 생성합니다. ")
st.button("Create Account", on_click=create_account)
if st.session_state.is_exist_account:
    account = st.session_state.account
    st.write("Public Key")
    st.success(f"{account.get('public_key').to_string().hex()}")
    st.write("Private Key")
    st.success(f"{account.get('private_key').to_string().hex()}")

#### DID 
if "is_exist_did" not in st.session_state:
    st.session_state.is_exist_did = False
    st.session_state.did = ""

def create_did():
    if st.session_state.is_exist_account:
        account = st.session_state.account
        public_key = account.get("public_key", "").to_string().hex()
        st.session_state.did = kernel.create_did("dkei", public_key)
        st.session_state.is_exist_did = True
    else:
        st.warning("Account 를 먼저 생성하세요")

st.subheader("DID 를 생성합니다.")
st.button("Create DID", on_click=create_did)
if st.session_state.is_exist_did:
    st.success(st.session_state.did)

#### DID Document
if "is_exist_did_doc" not in st.session_state:
    st.session_state.is_exist_did_doc = False
    st.session_state.did_doc = ""

def create_did_doc():
    if st.session_state.is_exist_account and st.session_state.is_exist_did:
        account = st.session_state.account
        public_key = account.get("public_key", "").to_string().hex()
        did = st.session_state.did
        st.session_state.did_doc = kernel.create_did_doc(did, public_key)
        st.session_state.is_exist_did_doc = True
    else:
        if st.session_state.is_exist_account == False:
            st.warning("Account 를 먼저 생성하세요")
        else:
            st.warning("DID 를 먼저 생성하세요")

st.subheader("DID Document 를 생성합니다.")
st.button("Create DID Document", on_click=create_did_doc)
if st.session_state.is_exist_did_doc:
    st.success(st.session_state.did_doc)
```

### VDR 에 저장하기

Issuer 기본 페이지가 완성되었으므로, 생성된 DID 와 DID Document 를 VDR 에 저장합니다.

```python
from requests.models import Response
import requests

api_url = "http://localhost:8000"

def call_register(_did:str, _document:str) -> Response:
    endpoint = f"{api_url}/register"
    params = {"_did": _did, "_document": _document}
    return requests.post(endpoint, params=params)

def save_to_vdr():
    if st.session_state.is_exist_did and st.session_state.is_exist_did_doc:
        did = st.session_state.did
        did_doc = st.session_state.did_doc        
        resp = call_register(did, did_doc)
        if resp.status_code == 200:
            result = resp.json()
            st.success(f"Register Result : {result}")
        else:
            st.warning(f"[ERROR] {resp}")

st.subheader("VDR 에 저장합니다.")
st.button("Register DID, DID Document", on_click=save_to_vdr)
```

위와 같이 작성하면, save_to_vdr 함수에서 결과가 페이지 최상단에 표기됩니다. 
![save_to_vdr_1](/assets/images/2024-04-17-save-to-vdr-1.png){: .align-center}
<p style="text-align: center;">save_to_vdr 함수에서 결과가 출력될 때</p>

페이지 최하단에 출력되도록 save_to_vdr 함수 결과를 session_state 에 저장하고, 하단에서 출력되도록 수정합니다. 

```python
from requests.models import Response
import requests

api_url = "http://localhost:8000"

if "is_saved" not in st.session_state:
    st.session_state.is_saved = False
    st.session_state.saved_result = ""

def call_register(_did:str, _document:str) -> Response:
    endpoint = f"{api_url}/register"
    params = {"_did": _did, "_document": _document}
    return requests.post(endpoint, params=params)

def save_to_vdr():
    if st.session_state.is_exist_did and st.session_state.is_exist_did_doc:
        did = st.session_state.did
        did_doc = st.session_state.did_doc        
        resp = call_register(did, did_doc)
        if resp.status_code == 200:
            result = resp.json()
            st.session_state.saved_result = result
            st.session_state.is_saved = True
        else:
            st.session_state.saved_result = resp

st.subheader("VDR 에 저장합니다.")
st.button("Register DID, DID Document", on_click=save_to_vdr)
result = st.session_state.saved_result
if st.session_state.is_saved:
    st.success(f"Register Result : {result}")
elif st.session_state.is_saved == False and st.session_state.saved_result != "":
    st.error(f"Error : {result}")
```
![save_to_vdr_2](/assets/images/2024-04-17-save-to-vdr-2.png){: .align-center}
<p style="text-align: center;">save_to_vdr 함수 결과를 별도 처리할 때</p>

> 코드 마지막 부분에서 if...else 로 처리하지 않고, elif 로 처리한 이유는 if...else 로 처리하면, 페이지 로깅시 is_saved 는 항상 False 이므로, Error 부분이 노출된 상태로 시작되므로, 매끄러워 보이지 않았습니다. 따라서, 페이지를 좀 더 깔끔하게 표현하기 위한 부분일 뿐 if...else 로 처리해도 로직상 아무런 문제가 없습니다. 
![if_else_processing](/assets/images/2024-04-17-if-else-processing.png)
{: .notice--info} 
