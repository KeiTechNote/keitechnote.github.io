---
layout: single
title: 프라이빗 이더리움 셋업
date: 2024-02-12 05:30 +09:00
published: true
categories: [dev]
tag: [Dev, Blockchain, ethereum, 블록체인, 이더리움, private ethereum, 프라이빗 이더리움]
toc: true
author_profile: false
---

**[주의사항]** 
* 설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
* 프라이빗 이더리움은 <U>go-ethereum 1.10.26</U> 버전을 기준으로 합니다. 
{: .notice--danger} 

### 개요

로컬에 이더리움 환경을 구성하기 위해, `go-ethereum (geth)` 를 사용합니다.
geth 는 Go 언어로 작성된 이더리움 노드로 쉽게 이더리움 네트워크에 접속해 동작되는 노드를 구성할 수 있습니다. 

### 사전 프로그램 설치

프라이빗 이더리움을 위해 `go-ethereum` 을 설치하고, `genesis.json` 을 작성해야 합니다. 
이를 위해 필요한 과정은 다음 Post 를 참고하기 바랍니다.

- [go-ethereum 설치하기](https://keitechnote.github.io/dev/dev-install-go-ethereum/)
- [프라이빗 이더리움 구성하기](https://keitechnote.github.io/dev/dev-install-private-ethereum/)

### 프라이빗 이더리움 실행하기

배포를 위한 프라이빗 이더리움을 실행합니다. 
이때, 외부에서 접속할 수 있도록 옵션을 추가합니다. 접속하려는 외부는 프라이빗 이더리움에 직접 console 로 접근할 사용자와 배포할 truffle이 됩니다. 외부에서 접속하는 방법은 `http`, `rpc`, `ws` 등이 있습니다. 그 중 http 접속을 설정합니다. 
직접 콘솔 접속 후, 계정을 생성하거나, 트랜잭션을 살펴보는 등 사용하려면, 필요한 모듈을 실행시 추가해야 합니다.
따라서, 다음과 같이 실행합니다. 

- 명령어 : `geth --http --http.api admin,debug,web3,eth,net,miner,personal,rpc --nodiscover --datadir .\chaindata`

![running_private_ethereum](/assets/images/2024-02-12-running-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 실행</p>


### 프라이빗 이더리움 콘솔 접속하기

실행된 프라이빗 이더리움을 살펴보면, 다음 화면에서 진행되지 않는다는 것을 알 수 있습니다. 

![warn_private_ethereum](/assets/images/2024-02-12-warn-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 Warning</p>

Warn 문구를 보면, etherbase 가 지정되지 않아 발생했습니다. 이때, etherbase 는 노드가 마이닝을 할때, 보상으로 받는 이더를 보관할 주소를 의미합니다. 즉, 마이닝 노드의 지갑 주소가 필요합니다. 따라서, 프라이빗 이더리움에 접속해 etherbase 를 지정해야 합니다. 
터미널을 열어 프라이빗 이더리움에 접속합니다. 

- 명령어 : `geth attach`

![attach_private_ethereum_1](/assets/images/2024-02-12-attach-private-ethereum-1.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 접속</p>

터미널에 출력되는 내용 중 `modules`는 콘솔 접속시 사용할 수 있는 모듈 목록입니다. 해당 모듈 목록은 프라이빗 이더리움 실행시 `--http.api` 와 함께 입력했던 목록들입니다. 

> **modules**
- `admin` : 노드 연결 목록, 현재 노드 정보 등 관리자 기능을 제공합니다. 
- `eth` : 이더리움 네트워크와 상호작용을 통해 현재 블록번호 확인, 잔액 조회 등의 기능을 제공합니다. 
- `personal` : 계정 생성 등 계정 관리 기능을 제공합니다. 
- `miner` : Mining 에 관련된 기능을 제공합니다. 
- `web3` : eth 모듈의 기능을 web3 라이브러리를 통해 제공합니다. 

### 계정 생성하기

- 명령어 : `eth.accounts`

현재 프라이빗 이더리움 내 계정을 확인합니다. 만약 생성된 계정이 없다면 계정을 생성합니다. 

- 명령어 : `personal.newAccount()`

![new_account_private_ethereum](/assets/images/2024-02-12-new-account-private-ethereum-1.png){: .align-center}
<p style="text-align: center;">계정 생성</p>

`Passphrase` 를 입력하면 계정이 생성되며, 계정 생성 사실은 프라이빗 이더리움에서도 확인할 수 있습니다. 
또한 생성된 계정 정보는 `~\eth\chaindata\keystore` 에 저장됩니다. 

![saved_account_private_ethereum](/assets/images/2024-02-12-new-account-private-ethereum-2.png){: .align-center}
<p style="text-align: center;">계정 저장</p>

### etherbase 지정하기

etherbase 는 Miner 주소이므로 `miner` 모듈을 사용해 지정합니다. 

- 명령어 : `miner.setEtherbase(eth.accounts[0])`

miner 모듈을 통해 etherbase 를 지정하면 eth 모듈로 `coinbase` 를 확인하면 등록되었음을 알 수 있습니다. 

![set_etherbase_private_ethereum](/assets/images/2024-02-12-set-etherbase-private-ethereum.png){: .align-center}
<p style="text-align: center;">etherbase 지정</p>

정상 등록이 완료되었다면, 이제 Mining 이 가능합니다. Mining 을 시작합니다.

- 명령어 : `miner.start(1)`

Mining 을 프라이빗 이더리움이 구성된 로컬에서 실행하면, 사용중인 PC 의 대부분의 리소스를 사용하게 됩니다. 
따라서, Mining 여부만을 확인하기 위해 스레드 수를 나타내는 "1" 을 지정합니다. 

![start_mining_private_ethereum](/assets/images/2024-02-12-start-mining-private-ethereum.png){: .align-center}
<p style="text-align: center;">Mining 시작</p>

Mining 이 동작하는 것을 확인했다면, Mining 을 종료합니다. 

- 명령어 : `miner.stop()`

프라이빗 이더리움도 종료합니다. 

### 프라이빗 이더리움 실행하기 (with Mining)

프라이빗 이더리움을 실행할 때마다 Mining 되도록 하기 위해 다음과 같이 실행합니다. 

- 명령어 : `geth --datadir .\chaindata --nodiscover --mine`

터미널에 Mining 상태가 출력되는 양을 `--verbosity <1~3>` 로 조절할 수도 있습니다. 

- 명령어 : `geth --datadir .\chaindata --nodiscover --mine --verbosity 1`
