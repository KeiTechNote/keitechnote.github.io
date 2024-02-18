---
layout: single
title: 스마트 컨트랙트용 API 서버 만들기
date: 2024-02-18 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, 블록체인, 이더리움, 스마트 컨트랙트, SSI, DID, API, API 서버, Python, 파이썬]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

Ganache 또는 프라이빗 이더리움과 같이 블록체인 네트워크에 컨트랙트를 배포한 경우, 

- [Ganache GUI에 간단한 VDR 스마트 컨트랙트 배포하기](https://keitechnote.github.io/vdr/vdr-deploy-ganache-gui/)
- [프라이빗 이더리움에 간단한 VDR 스마트 컨트랙트 배포하기](https://keitechnote.github.io/vdr/vdr-deploy-private-ethereum/)

상호작용하는 가장 간단한 방법은 배포된 컨트랙트를 직접 호출해 사용하는 것입니다. 하지만, 어플리케이션 입장에서 컨트랙트의 세부정보를 알 필요가 없고,단지 호출해서 원하는 기능을 사용할 수 있으면 됩니다. 이러한 기능을 수행해 주는 것을 API 서버라고 합니다. 
즉, 어플리케이션은 API 서버만 호출하면, API 서버가 컨트랙트와의 모든 동작을 대행해 주는 것입니다.

### 사전 프로그램 설치

API 서버를 구성하는 방법은 다양하지만, 본 Post 에서는 파이썬과 FastAPI 를 사용해 API 서버를 구성합니다.

- [Python 설치하기](https://keitechnote.github.io/dev/dev-install-python/)
- [FastAPI 설치하기](https://keitechnote.github.io/dev/dev-install-fastapi/)

#### 블록체인 네트워크 실행하기

프라이빗 이더리움, Ganache 등 사용할 이더리움 네트워크를 실행합니다. 본 Post 에서는 좀 더 직관적인 Ganache GUI를 사용합니다. 

> Ganache GUI 를 설치하지 않았다면,[Ganache GUI 설치하기](https://keitechnote.github.io/dev/dev-install-ganache-gui/) 를 참고해 설치하기 바랍니다.
{: .notice--info}

#### 스마트 컨트랙트 배포하기

Ganache GUI 에 VDR 스마트 컨트랙트를 배포합니다. 배포된 Ganache GUI 가 있다면, 이 단계는 넘어가도 좋습니다.  

> Ganache GUI 에 배포하지 않았다면,  [Ganache GUI에 간단한 VDR 스마트 컨트랙트 배포하기](https://keitechnote.github.io/vdr/vdr-deploy-ganache-gui/)를 참고해 배포하기 바랍니다.
{: .notice--info}

### API 서버 만들기 

블록체인 네트워크와 파이썬 API 간 통신에는 Web3 라이브러리를 사용합니다. 라이브러리를 설치합니다. 

- 명령어 : `pip install web3`

![install_web3_library](/assets/images/2024-02-18-install-web3-library.png){: .align-center}
<p style="text-align: center;">파이썬 web3 라이브러리 설치</p>

스마트 컨트랙트와 통신하는 API 코드를 작성합니다.

```python
from fastapi import FastAPI, HTTPException
from web3 import Web3

import json

app = FastAPI()

# Ganache GUI에 배포된 스마트 컨트랙트 주소
contract_address = "스마트 컨트랙트 주소"

# Ganache GUI에서 제공하는 HTTP RPC 엔드포인트
ganache_rpc_url = "http://localhost:8545"

# Web3 객체 생성
web3 = Web3(Web3.HTTPProvider(ganache_rpc_url))

# 스마트 컨트랙트 ABI (Application Binary Interface) - 컴파일된 스마트 컨트랙트의 인터페이스
abi_file_path = r"<simpleVDR.json 파일 경로>"
with open(abi_file_path, "r", encoding="utf-8") as fp:
    contracts = json.load(fp)

contract_abi = contracts.get("abi", [])

# 컨트랙트 객체 생성
contract = web3.eth.contract(address=contract_address, abi=contract_abi)

@app.post("/register")
async def register_did(_did: str, _document: str):
    try:
        # 스마트 컨트랙트의 등록 함수 호출
        transaction = contract.functions.register(_did, _document).transact({'from': web3.eth.accounts[0]})
        web3.eth.waitForTransactionReceipt(transaction)
        return {"message": "String registered successfully"}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

@app.get("/resolver/{_did}")
async def resolve_did(_did: str):
    try:
        # 스마트 컨트랙트의 조회 함수 호출
        result = contract.functions.resolve(_did).call()
        return {"registered_string": result}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))

```

코드내 일부 값은 자신의 환경에 맞는 값을 기입합니다. 

> **설정값**
- `contract_address` : 배포된 스마트 컨트랙트 주소
- `ganache_rpc_url` : Ganache GUI IP 와 Port
- `abi_file_path` : 스마트 컨트랙트 빌드 후 생성된 json 파일 경로 

### API 서버 구동하기 

API 서버를 실행합니다.

- 명령어 : `uvicorn vdr:app --host 127.0.0.1 --port 8000 --reload`

![running_api_server_vdr](/assets/images/2024-02-18-running-api-server-vdr.png){: .align-center}
<p style="text-align: center;">VDR용 API 서버 실행</p>

웹 브라우저로 Register 와 Resolver 경로에 접속합니다. 

![running_api_server_register](/assets/images/2024-02-18-running-api-server-register.png){: .align-center}
<p style="text-align: center;">register 경로 접속</p>

![running_api_server_resolver](/assets/images/2024-02-18-running-api-server-resolver.png){: .align-center}
<p style="text-align: center;">resolver 경로 접속 </p>

화면에 출력되는 값은 예상했던 값이 아닙니다. 이는 API 통신을 위해 필요한 값을 약속한 방식으로 전달하지 않았기에 API 서버에서 발생하는 에러입니다.따라서, API 함수를 호출하는 별도 테스트 코드가 필요합니다. 

### API 서버 동작 테스트하기 

테스트 코드를 작성합니다. 

- 위치 : `test.py`

```python
import requests

# API 서버 주소 
api_server = "http://127.0.0.1:8000"

def test_register():
    register_url = f"{api_server}/register"
    params = {"_did" : "56789", "_document" : "sample document"}
    resp = requests.post(register_url, params=params)
    response_data = resp.json()
    print(response_data)

def test_resolve():
    did = "56789"
    resolver_url = f"{api_server}/resolve/{did}"
    resp = requests.get(resolver_url)
    response_data = resp.json()
    print(response_data)
    

if __name__ == "__main__":
    test_register()
    test_resolve()
```

테스트 코드를 실행합니다.

- 명령어 : `python test.py`

정상 동작되었다면 다음과 같은 화면을 볼 수 있습니다. 

![execute_test_code](/assets/images/2024-02-18-execute-test-code.png){: .align-center}
<p style="text-align: center;">테스트 코드 실행</p>

모든 동작은 Ganache GUI 에서 확인할 수 있습니다. 

![test_code_block_info](/assets/images/2024-02-18-test-code-block-info.png){: .align-center}
<p style="text-align: center;">테스트 코드 실행 - Block 정보</p>

![test_code_tx_info](/assets/images/2024-02-18-test-code-tx-info.png){: .align-center}
<p style="text-align: center;">테스트 코드 실행 - Tx 정보</p>

![test_code_contracts_info](/assets/images/2024-02-18-test-code-contracts-info.png){: .align-center}
<p style="text-align: center;">테스트 코드 실행 - Contracts 정보</p>