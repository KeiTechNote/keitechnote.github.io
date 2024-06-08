---
layout: single
title: Holder 웹페이지 만들기
date: 2024-05-10 05:30 +09:00
published: true
categories: [holder]
tag: [Dev, SSI, DID, Python, FastAPI, 파이썬, Streamlit, 스트림릿, DID Document, ECDSA, DID Syntax, 구문, DID 구문, method-name, method-specific-id, DID-Core]
toc: true
author_profile: false
---

### 개요

Verifier 는 VDR 에 저장된 DID Document 를 활용


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