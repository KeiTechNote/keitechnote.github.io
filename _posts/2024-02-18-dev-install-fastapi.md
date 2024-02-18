---
layout: single
title: FastAPI 설치하기
date: 2024-02-18 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, Python, FastAPI, API, API서버, 파이썬]
toc: true
author_profile: false

---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

웹(프론트엔드)과 서버(백엔드)간 상호 통신하기 위해 주로 API (Application Programming Interface) 를 사용합니다. 
파이썬으로 API 를 만드는 방법은 여러가지가 있습니다. 
그 중 FastAPI 로 API 를 만들겠습니다. 

### 라이브러리 설치하기

FastAPI 는 파이썬 패키지로 여타 파이썬 라이브러리를 설치하는 방법과 동일하게 `pip` 명령어로 설치합니다.

- 명령어 : `pip install fastapi`

![install_fastapi](/assets/images/2024-02-18-install-fastapi.png){: .align-center}
<p style="text-align: center;">FastAPI 설치</p>

웹에서 API 를 호출하려면, API 는 접근 가능해야 합니다. 여기서 "접근 가능" 이라함은 "서버"를 의미합니다.
FastAPI 가 API 개발을 위해 필요한 라이브러리라면, `Uvicorn" 이 서버 역할을 합니다. 
따라서, API 란 "기능 + 서버" 를 결합해야 동작하기에 "API = API 서버" 를 지칭하기도 합니다.   
Uvicorn 을 설치합니다.

- 명령어 : `pip install uvicorn`

![install_uvicorn](/assets/images/2024-02-18-install-uvicorn.png){: .align-center}
<p style="text-align: center;">Uvicorn 설치</p>

### API 서버 만들기

API 서버를 위해 테스트 코드를 작성합니다. 

- 위치 : test.py

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
	return {"message": "Hello World"}

@app.get("/hello/{name}")
async def say_hello(name: str):
	return {"message": f"Hello {name}"}
```

Uvicorn 으로 API 서버를 실행합니다.

- 명령어 : `uvicorn <파일명>:<FastAPI 인스턴스명> --host <서버 IP> --port <서버 Port> --reload`

> **설정값**
- `파일명` : 파이썬 파일명 (예: test)
- `FastAPI 인스턴스명` : FastAPI 인스턴스명 (예: app)
- `host` : 서버 IP 주소 (예: 127.0.0.1)
- `port` : 서버 Port (예: 8000)
- `reload` : 파이썬 코드 변경시 즉시 반영되는 옵션 (Hot Reload)

![running_api_server](/assets/images/2024-02-18-running-api-server.png){: .align-center}
<p style="text-align: center;">API 서버 실행</p>

웹 브라우저에서 API 서버 주소 (127.0.0.1:8000) 으로 접속합니다. 

![connect_api_server_1](/assets/images/2024-02-18-connect-api-server-1.png){: .align-center}
<p style="text-align: center;">API 서버 접속 (1)</p>

![connect_api_server_2](/assets/images/2024-02-18-connect-api-server-2.png){: .align-center}
<p style="text-align: center;">API 서버 접속 (2)</p>

### 추가 기능

FastAPI 설치만으로 API 문서를 자동 작성하고, 테스트할 수 있는 Redoc 과 Swagger 페이지를 제공합니다. 

#### Redoc 접속

Redoc 은 반응형 웹으로 다양한 기기에서 접속해 확인하기 용이합니다. 

- Redoc 경로 : `https://<서버 IP>:<서버 Port>/redoc` (예: https://127.0.0.1:8000/redoc)

![connect_api_server_redoc](/assets/images/2024-02-18-connect-api-server-redoc.png){: .align-center}
<p style="text-align: center;">API 서버 redoc 접속</p>

#### Swagger 접속

Swagger 는 인터랙티브(상호작용하는) 웹으로 API 를 테스트해 보기 용이합니다.

- Swagger 경로 : `https://<서버 IP>:<서버 Port>/docs` (예: https://127.0.0.1:8000/docs)

![connect_api_server_swagger](/assets/images/2024-02-18-connect-api-server-swagger.png){: .align-center}
<p style="text-align: center;">API 서버 Swagger 접속</p>


### 참고

- FastAPI Official Docs : [https://fastapi.tiangolo.com/tutorial/](https://fastapi.tiangolo.com/tutorial/)