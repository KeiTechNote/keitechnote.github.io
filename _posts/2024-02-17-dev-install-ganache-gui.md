---
layout: single
title: Ganache GUI 설치하기
date: 2024-02-17 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum, 가나쉬, Ganache, Ganache GUI]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

Ganache 는 TruffleSuite 를 설치하면 함께 설치되는 가상 이더리움 환경입니다. CLI 버전이 설치되지만, GUI 버전도 별도 제공하고 있습니다. 
GUI 버전의 Ganache 를 설치하고, 인터페이스를 살펴 보겠습니다. 

### 다운로드

Ganache 사이트에 접속해 OS 별 설치파일을 다운로드 합니다. 

- Ganache GUI : [다운로드](https://trufflesuite.com/ganache/)

![download_ganache_gui](/assets/images/2024-02-17-download-ganache-gui.png){: .align-center}
<p style="text-align: center;">OS별 Ganache GUI 다운로드</p>

### 설치하기

보통의 프로그램 설치 방식과 동일하게 설치합니다. 

![install_ganache_gui](/assets/images/2024-02-17-install-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache GUI 설치</p>

> macOSX 에서 Ganache GUI 설치시 상당히 오랜시간이 소요됩니다.
{: .notice--info}

### 살펴보기

Ganache GUI 는 이더리움 네트워크를 구성하는 "Quick Start" 와  "NEW Workspace" 를 제공합니다. 
두 방식 모두 Ethereum 과 FileCoin 을 지원합니다. 

#### Quick Start 

Quick Start 는 Ganache 에서 사전에 지정한 기본값으로 Ethereum 또는 FileCoin 네트워크를 구성할 수 있습니다. 

![quick_start_ganache_gui](/assets/images/2024-02-17-quick-start-ganache-gui.png){: .align-center}
<p style="text-align: center;">Quick Start 실행 화면</p>

실행 화면에서 살펴볼 수 있는 정보는 다음과 같습니다. 

> **Ganache GUI 정보**
- `Current Block` : Mining 블록 수
- `Gas Price` : 가스 비용
- `Gas Limit` : 가스 최대 허용량
- `HardFork` : 이더리움 하드포크 코드명 (적용된 옵션을 알 수 있습니다.)
- `Network ID` : ChainId
- `RPC Server` : 서버 접속 정보 (RPC, IP, Port)
- `Mining Status` : Minging 동작 여부
- `Workspace` : 동작 환경명

이외에도 `Save` 로 현재 환경을 저장하고, `Switch` 로 다른 환경으로 변경하거나, `options` 로 이러한 정보를 바꿀 수 있습니다. 
또한, MNEMONIC 정보나 각 100이더가 보관된 계정 10개의 주소와 개인키를 확인할 수 있습니다. 

#### New Workspace 

New Workspace 는 Ganache Workspace 이름, IP, Port 번호, network id 등 서버 정보, 계정, 가스 등 실행화면에서 살펴볼 수 있는, 블록체인 네트워크를 생성할 때 필요한 값을 설정해 나만의 블록체인을 만들 수 있습니다. 하지만 이러한 설정 값은 Quick Start 이후 옵션 화면에서도 설정할 수 있는 부분이므로, Quick Start 로 블록체인 네트워크를 빠르게 구성한 후 원하는 부분만 필요시 수정하는 것을 좀 더 편리합니다. 


### 참고

- Ganache Official Docs : https://trufflesuite.com/docs/ganache/quickstart/

