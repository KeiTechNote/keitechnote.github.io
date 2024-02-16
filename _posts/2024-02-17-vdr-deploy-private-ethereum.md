---
layout: single
title: 프라이빗 이더리움에 간단한 VDR 스마트 컨트랙트 배포하기
date: 2024-02-17 00:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID, deploy]
toc: true
author_profile: false
---

### 개요

[Ganache 환경에 간단한 VDR 스마트 컨트랙트 배포하기](https://keitechnote.github.io/vdr/vdr-simple-vdr-truffle/)를 통해 로컬 개발환경을 구성하고, 개발된 스마트 컨트랙트에 대한 테스트 및 Ganache 에 배포하는, 일련의 개발 사이클을 수행해 보았습니다. 이제 프라이빗 이더리움상에 배포해 보겠습니다. 
이를 위해선 프라이빗 이더리움 구성이 선행되어야 합니다. 

### 사전 프로그램 설치

프라이빗 이더리움은 로컬 PC 환경에 구성하고, 이를 위해 `go-ethereum (geth)`를 사용합니다. 
geth 는 Go 언어로 작성된 이더리움 클라이언트로 쉽게 이더리움 네트워크에 접속하거나 구동할 수 있습니다. 
필요한 설치 과정은 다음 Post 를 참고하기 바랍니다.

- [go-ethereum 설치하기](https://keitechnote.github.io/dev/dev-install-go-ethereum/)
- [프라이빗 이더리움 구성하기](https://keitechnote.github.io/dev/dev-install-private-ethereum/)
- [프라이빗 이더리움 셋업](https://keitechnote.github.io/dev/dev-setup-private-ethereum/)

### 배포 준비하기

Truffle 환경에서 배포할 네트워크는 `truffle-config.js` 에서 지정합니다. 

- 위치 : `~\truffle\truffle-config.js`

![truffle_config](/assets/images/2024-02-17-truffle-config.png){: .align-center}
<p style="text-align: center;">truffle-config.js 네트워크 설정 부분</p>

배포할 네트워크명을 지정한 후 필요한 정보를 설정합니다. 

```js
(생략)
networks: {
    (생략)
    private: {
        host: "연결할 노드 URL or IP",
        port: 연결할 노드 Port,
        network_id: "블록체인 네트워크 ChainId"
    }
}
```

설정에 필요한 정보는 프라이빗 이더리움을 실행 설정값입니다. 따라서, 프라이빗 이더리움을 실행하면서 원하는 값을 부여하면 됩니다. 

- 추가 설정값 #1 : `--http --http.port 9545 --networkid 15`

```js
(생략)
networks: {
    (생략)
    private: {
        host: "127.0.0.1",
        port: 9545,
        network_id: "15"
    }
}
```

프라이빗 이더리움에 접속해 스마트 컨트랙트를 배포합니다. 컨트랙트는 트랜잭션으로 작성되어 발송인 서명 후 배포됩니다. 
이를 위해 `발송인 주소` 와 서명을 위한 `PrivateKey` 를 알아야 합니다. 
현재 Miner 계정만 있으므로, Miner 계정을 발송인으로 지정하면 다음과 같은 설정값이 추가됩니다. 

- 추가 설정값 #2 : `--unlock <Miner 주소> --password <Miner privatekey 파일 경로> --allow-insecure-unlock`

Miner `PrivateKey` 는 직접 입력하지 않고, 텍스트 파일에 Privatekey를 저장한 후 그 경로를 지정합니다. 
추가로, 블록체인 노드는 실행시 기본값으로 계정의 갖고 있는 이더(ether)를 사용(송금, 가스비 지불 등)할 수 없도록 차단되어 있습니다. 이를 `--allow-insecure-unlock` 옵션으로 해제합니다. 

프라이빗 이더리움을 실행할 최종 명령어는 다음과 같습니다. 

- 명령어 : `geth --http --http.port 9545 --networkid 15 --unlock 0x20639eB849F4e0C35C5900e34d94F34A962f8551 --password .\chaindata\keystore\miner_privatekey.txt --allow-insecure-unlock --datadir .\chaindata --mine --miner.threads=1`

![standby_private_ethereum](/assets/images/2024-02-17-stand-by-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 준비</p>

### 배포하기

truffle 디렉토리에서 다음의 명령어로 배포합니다. 

- 명령어 : `truffle migrate --network private`

![deploy_simple_vdr_private_ethereum](/assets/images/2024-02-17-deploy-simple-vdr-private-ethereum.png){: .align-center}
<p style="text-align: center;">스마트 컨트랙트 배포</p>

