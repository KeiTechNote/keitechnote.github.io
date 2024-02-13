---
layout: single
title: go-ethereum 설치하기
date: 2024-02-12 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

`go-ethereum` (이하 geth) 는 Go 언어로 개발되어, 실제 운영되는 이더리움 노드를 구성할 수 있는 프로그램입니다. 
현재 이더리움은 POW 와 POS 의 과도기 상태이므로, POW 로 동작하는 geth를 설치합니다. 
geth 는 cli (콘솔 명령어) 만 이용할 수 있으며, 업그레이드 기능을 제공하지 않기 때문에, 필요시 신규 버전을 재설치해야 합니다. 

### 다운로드

현재 대부분의 OS 에서 설치할 수 있도록 설치 파일을 제공하고 있으므로 자신의 환경에 맞는 프로그램을 다운받아 설치를 진행합니다.

- geth 다운로드 : https://geth.ethereum.org/downloads

![geth 다운로드](/assets/images/2024-02-12-geth-download.png){: .align-center}
<p style="text-align: center;">Go-Ethereum 다운로드</p>

> 윈도우의 경우 설치시 다음과 같은 경고 화면 나타나고, 설치 버튼은 확인할 수 없습니다. 이 경우, "추가정보" 를 선택하면, 설치 버튼을 확인할 수 있습니다. 
![윈도우 경고](/assets/images/2024-02-12-geth-install-windows-warning.png){: .align-center}
<p style="text-align: center;">윈도우 경고 화면</p>

### 설치

다운로드가 완료되었다면, 자신이 원하는 경로를 지정해 설치를 진행합니다. 지정한 경로는 Geth 프로그램이 저장되는 디렉토리이며,
기본 경로는 다음과 같습니다.

- 기본 설치 경로 : C:\Programs\Geth

![geth 프로그램](/assets/images/2024-02-12-geth-default-directory.png){: .align-center}
<p style="text-align: center;">설치된 geth 프로그램</p>

> macOSX 는 별도 설치과정 없이 압축 해제 후 바로 실행할 수 있습니다. 

하지만 이더리움 블록, 계정 등 이더리움 동작에 필요한 정보를 저장하는 공간은 별도 디렉토리에 저장되며, 다음과 같습니다. 

- Windows 홈 디렉토리 : `~/AppData/Local/Ethereum` or `~/AppData/Roaming/Ethereum`
    
![홈 디렉토리 구조](/assets/images/2024-02-12-geth-directory-tree.png){: .align-center}
<p style="text-align: center;">홈 디렉토리 구조</p>

> 다른 운영체제의 홈 디렉토리는 다음과 같습니다. 
- Linux : `~/.ethereum`
- macOSX : `~/Library/Ethereum`
{: .notice--info} 


### 실행

Geth 는 실행시 다양한 옵션을 설정할 수 있습니다. 별도의 설정없이 `geth` 명령어만 실행하면, 이더리움 메인넷에 연결해 실제 이더를 채굴할 수도 있고, Goerli, Sepolia 와 같은 테스트넷에 연결해 개발된 스마트 컨트랙트를 테스트넷에 공개할 수도 있습니다. 
또는 다양한 옵션을 부여해 프라이빗 네트워크를 구성할 수도 있습니다. 

- 메인넷 : `geth`
- 테스트넷 (예: Sepolia) : `geth --sepolia`

> 프라이빗 네트워크를 별도 Post 에서 소개합니다.


### 동기화 모드

별도 설정없이 geth 를 실행하면, `Fast-Sync` 모드로 동기화를 수행합니다. 
동기화 모드란, 이더리움이 메인넷에 연결되어 이웃 노드로 부터 블록 정보와 트랜잭션을 가져오는 것을 의미합니다. 
이때, 가져온 블록과 트랜잭션이 올바른지 검증을 하게 되며, 모드에 따라 가져오는 블록과 트랜잭션 수가 다르며, 그에 따라 검증 방법도 다릅니다.

- Fast-Sync
    - 동기화 데이터 : Block Header + Block Body + 최근 1024개 트랜잭션
    - 검증 방법 : 최근 1024개 트랜잭션을 연산해 생성된 블록이 올바른지 재검증
- Full-Sync
    - 동기화 데이터 : Block Header + Block Body + 지금까지 발생한 모든 트랜잭션
    - 검증 방법 : 지금까지 방생한 모든 트랜잭션을 연산해 생성된 블록이 올바른지 모두 재검증
- Light-Sync
    - 동기화 데이터 : Block Header
    - 검증 방법 : 없음

처음 geth 를 실행하면, 기본적으로 Fast-Sync 모드로 동기화를 수행합니다. 하지만, 설정된 수준의 블록과 트랜잭션을 받지 않은 상태에서 geth 가 종료된 후 재시작되면, geth 는 Full-Sync 모드로 변경됩니다. 이 경우, 다시 Fast-Sync 모드로 실행하기 위해선 geth 의 홈 디렉토리 내 `datadir` 디렉토리를 삭제하면 됩니다. 
