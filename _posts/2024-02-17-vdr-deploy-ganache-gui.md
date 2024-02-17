---
layout: single
title: Ganache GUI에 간단한 VDR 스마트 컨트랙트 배포하기
date: 2024-02-17 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID, deploy, Ganache GUI, 가나쉬]
toc: true
author_profile: false
---

### 개요

[Ganache 환경에 간단한 VDR 스마트 컨트랙트 배포하기](https://keitechnote.github.io/vdr/vdr-simple-vdr-truffle/)에서 Ganache CLI 에 배포해 보았습니다. 별도 설정없이 사용할 수 있고, VSCode 하단 터미널 창을 사용해 한 화면에서 모두 처리할 수 있다는 점은 매력적인 부분입니다. 하지만, CLI 환경이 익숙하지 않다면 그 또한 하나의 장벽이 될 수 있습니다. 따라서, 별도 제공되는 Ganache GUI 에 배포해 보겠습니다. 
이를 위해선 Ganache GUI 설치가 선행되어야 합니다 .


### 사전 프로그램 설치

Ganache GUI 는 보통 프로그램 설치하는 절차와 동일해 별 어려움 없이 설치할 수 있습니다. 

- [Ganache GUI 설치하기](https://keitechnote.github.io/dev/dev-install-ganache-gui/)

### Ganache GUI 실행 및 수정하기

Ganache GUI 를 실행합니다. Quick Start 등 원하는 블록체인을 구성합니다. 

> 하기 내용은 Quick Start 로 구성된 이더리움을 기반으로 작성되었습니다. 
{: .notice--info}

실행화면에서 보이는 블록체인 정보 중 서버 정보는 기본값 `127.0.0.1:7545` 로 설정됩니다. 

![main_info_ganache_gui](/assets/images/2024-02-17-main-info-ganache-gui-1.png){: .align-center}
<p style="text-align: center;">Ganache 정보</p>

해당 값은 Ganache CLI 와 같은 값으로 `truffle-config.js` 의 development 에서 확인할 수 있습니다.
따라서, Ganache GUI 를 동일한 값을 사용할 경우, Truffle 은 배포 대상을 식별할 수 없거나, Ganach CLI 를 대상으로 배포할 수 있습니다. 
이를 해결하기 위해 Port 를 수정합니다. 

- 위치 : Ganache GUI > Options (톱니바퀴 아이콘) > SERVER

해당 위치에서 Port 를 다른 값으로 수정한 후 상단의 `SAVE AND RESTART` 를 선택합니다. 

![modify_server_ganache_gui](/assets/images/2024-02-17-modify-server-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache 서버 포트 수정</p>

수정이 완료되었습니다. 

### 배포 준비하기

Ganache CLI 는 기본 설정값을 사용해 배포하기 때문에 바로 사용할 수 있지만, Ganache GUI 는 별도 설치된 프로그램이므로 Truffle 에서 Ganache GUI 로 배포 경로를 변경하도록 설정을 해야합니다. 

#### truffle-config.js 설정하기

Truffle 환경에서 배포할 네트워크는 `truffle-config.js` 에서 지정합니다. 

- 위치 : `~\truffle\truffle-config.js`

![truffle_config](/assets/images/2024-02-17-truffle-config.png){: .align-center}
<p style="text-align: center;">truffle-config.js 네트워크 설정 부분</p>

배포할 네트워크명을 지정한 후 필요한 정보를 설정합니다. 

```js
(생략)
networks: {
    (생략)
    ganache: {
        host: "연결할 노드 URL or IP",
        port: 연결할 노드 Port,
        network_id: "블록체인 네트워크 ChainId"
    }
}
```

설정에 필요한 정보는 Ganache GUI 실행화면에서 확인할 수 있습니다. 

![main_info_ganache_gui](/assets/images/2024-02-17-main-info-ganache-gui-2.png){: .align-center}
<p style="text-align: center;">Ganache 정보</p>

```js
(생략)
networks: {
    (생략)
    ganache: {
        host: "127.0.0.1",
        port: 8545,
        network_id: "5777"
    }
}
```

#### Ganache GUI 설정하기

Ganache GUI 와 Truffle 을 연결하는 것은 Ganache GUI 에 truffle-config.js 경로를 추가하는 것으로 완료됩니다. 

- 위치 : Ganache GUI > Options (톱니바퀴 아이콘) > WORKSPACE

`ADD PROJECT` 버튼으로 `Truffle Projects` 에 truffle-config.js 경로를 추가 후 `SAVE AND RESTART` 합니다. 

![modify_project_ganache_gui](/assets/images/2024-02-17-modify-project-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache Workspace 정보 수정</p>

배포를 위한 준비는 마무리되었습니다. 

### 배포하기 

터미널을 사용해 Truffle 디렉토리로 이동해 다음의 명령어를 실행합니다.

- 명령어 : `truffle mignate --network ganache`

![vdr_deploy_ganache_gui](/assets/images/2024-02-17-vdr-deploy-ganach-gui.png){: .align-center}
<p style="text-align: center;">Ganache GUI 에 스마트 컨트랙트 배포</p>

배포된 스마트 컨트랙트는 Ganache GUI 에서 트랜잭션과 블록 정보, 컨트랙트를 확인할 수 있습니다. 

- 위치 : Ganache GUI > BLOCKS

![block_info_ganache_gui](/assets/images/2024-02-17-block-info-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache GUI 블록 정보</p>

- 위치 : Ganache GUI > TRANSACTIONS

![tx_info_ganache_gui](/assets/images/2024-02-17-tx-info-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache GUI 트랜잭션 정보</p>

- 위치 : Ganache GUI > CONTRACTS

![contract_info_ganache_gui](/assets/images/2024-02-17-contract-info-ganache-gui.png){: .align-center}
<p style="text-align: center;">Ganache GUI 컨트랙트 정보</p>


