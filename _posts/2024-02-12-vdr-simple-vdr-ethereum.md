---
layout: single
title: 로컬 이더리움 환경에 간단한 VDR 스마트 컨트랙트 배포하기
date: 2024-02-12 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID, Ganache, deploy]
toc: true
author_profile: false
---

### 개요

[Ganache 환경에 간단한 VDR 스마트 컨트랙트 배포하기][vdr-simple-vdr-truffle]를 통해 로컬 개발환경을 구성하고, 개발된 스마트 컨트랙트에 대한 테스트 및 Ganache 에 배포하는, 일련의 개발 사이클을 수행해 보았습니다. 이제 서비스 제공을 위해 Publishing 을 수행하면 됩니다. 
Ganache 를 통해 이더리움 배포 전 점검은 완료되었습니다. 이제 로컬에 프라이빗 이더리움을 구성하고, 배포하겠습니다. 

### 사전 프로그램 설치

로컬에 이더리움 환경을 구성하기 위해, `go-ethereum (geth)` 를 사용합니다.
geth 는 Go 언어로 작성된 이더리움 노드로 쉽게 이더리움 네트워크에 접속해 동작되는 노드를 구성할 수 있습니다. 
필요한 설치 과정은 다음 Post 를 참고하기 바랍니다.

- [이더리움 설치하기](https://keitechnote.github.io/dev/dev-install-go-ethereum/)
- [프라이빗 이더리움 네트워크 구성하기](https://keitechnote.github.io/dev/dev-install-private-ethereum/)

### 프라이빗 이더리움 실행하기

배포를 위한 프라이빗 이더리움을 실행합니다. 
이때, 외부에서 접속할 수 있도록 옵션을 추가합니다. 외부에서 접속하는 방법은 `http`, `rpc`, `ws` 등이 있습니다. 
그 중 http 접속을 설정합니다. 

- 명령어 : `geth --datadir .\chaindata --http`

> 프라이빗 이더리움에 직접 접속(`attach`) 해 사용하길 원한다면, 다음의 옵션을 추가해 실행합니다.
`--http.api admin,debug,web3,eth,db,net,miner,personal,rpc,console`
{: .notice--info}

### 계정 생성하기 

스마트 컨트랙트를 배포하기 위해선, 스마트 컨트랙트에 서명하고 가스비를 제공하는 계정이 필요합니다. 
프라이빗 이더리움에 계정을 생성하는 방법은 크게 두 가지가 있습니다. 

1. genesis.json 의 alloc 에 설정하는 방법
2. geth 로 계정을 생성하는 방법

genesis.json 에 별도 주소를 설정하지 않았으므로 geth 를 사용해 계정을 생성합니다. 

- 명령어 : `geth account new`

Password 를 입력하면 계정 생성이 완료됩니다. 

![private_ethereum_new_account](/assets/images/2024-02-12-new-account-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 내 계정 생성</p>


### 배포 네트워크 설정하기

TruffleSuite 기반 환경에서 배포할 네트워크는 `truffle-config.js` 에서 설정합니다. 

- 위치 : truffle/truffle-config.js

설정에 필요한 정보는 다음과 같습니다.

- `host` : 프라이빗 이더리움 URL or IP
- `port` : 프라이빗 이더리움 Port
- `network_id` : 네트워크 ID
- `from` : 프라이빗 이더리움 내 Account (Public Address)

프라이빗 이더리움에 생성한 계정 (public address) 과 동작 로그에서 필요한 정보를 확인합니다. 

![running_private_ethereum](/assets/images/2024-02-12-running-private-ethereum.png){: .align-center}
<p style="text-align: center;">동작중인 프라이빗 이더리움</p>













[vdr-simple-vdr-truffle]: https://keitechnote.github.io/vdr/vdr-simple-vdr-truffle/