---
layout: single
title: 도커로 프라이빗 이더리움 구성하기
date: 2024-03-09 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum, Private Ethereum, 프라이빗 이더리움, go-ethereum, geth, 도커, Docker, 쿠버네티스, Kubernetes]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

도커를 사용하면, [로컬에 구성하는 것](https://keitechnote.github.io/dev/dev-install-private-ethereum/)보다 좀 더 관리가 편리한 프라이빗 이더리움을 구성할 수 있습니다. 
다만, 이전에는 1.10.26 버전을 사용해 단일 노드를 구성했다면, 본 Post 에서는 PoW / PoA 를 지원하는 최신버전인 1.13.14 를 사용하고, 멀티 노드를 구성합니다. 

### 사전 설치 프로그램

도커에 구성하기 위해, Docker Desktop 설치를 선행합니다. 설치에 필요한 과정은 다음 Post 를 참고하기 바랍니다.

- [Docker Desktop(Kubernetes) 설치하기](https://keitechnote.github.io/dev/dev-install-docker-desktop/)

### 도커 환경 구성하기 (공통)

도커는 필요한 프로그램이 설치된 서버를 압축한 `이미지(image)` 파일을 실행함으로써 손쉽게 원하는 환경을 구성할 수 있습니다. 
이미지 파일은 [Docker Hub](https://hub.docker.com/)와 같은 도커 이미지 저장소에 공개되어 있습니다. 
Docker Desktop 은 도커 이미지를 다운로드, 실행, 삭제 등을 손쉽게 관리할 수 있도록 CLI 명령어와 GUI 를 제공합니다. 
하지만 프라이빗 이더리움 구성은 CLI 명령어를 사용합니다. 

> Docker Hub 와 같은 공개된 도커 이미지 저장소는 Github 처럼 누구든 이미지를 업로드해 공유할 수 있습니다. 
다만, 공식 이미지 (Official Image) 를 포함한 대부분의 공개된 이미지들은 비교적 안전하지만, 일부 이미지들은 악성코드가 설치되어 있는 등 안전하지 않으므로, 비교적 평판이 높은(다운로드 수가 높은) 이미지를 사용하는 등 주의가 필요합니다. 
최신 Docker Desktop 은 `Docker Scout` 기능으로 도커 이미지가 취약점을 점검하는 등의 안전성을 확인할 수 있습니다. 
![docker_scout_on_docker_desktop](/assets/images/2024-03-09-docker-scout-on-docker-desktop.png){: .align-center}
{: .notice--warning}

#### 도커 실행하기

Docker Desktop을 실행합니다. 
CLI 명령어는 반드시 Docker Desktop 이 실행되어야 동작합니다. 

#### 이더리움 이미지 다운로드

도커에서는 이미지 다운로드를 `Pull` 이라고 합니다. 이더리움 이미지를 다운로드 합니다. 버전을 명시하지 않는다면, 항상 최신 버전이 다운로드 됩니다. 하지만 1.14.x 부터 PoS 만을 지원합니다. PoW 또는 PoA 를 사용해 프라이빗 이더리움을 구축하기 위해선, 1.13.x 버전을 사용해야 합니다. 
본 Post 를 작성시점을 기준으로 최신버전인 1.13.14 를 다운로드 합니다. 

- 명령어 : `docker pull ethereum/client-go:alltools-v1.13.14`

![pull_ethereum_image](/assets/images/2024-03-09-pull-ethereum-image.png){: .align-center}
<p style="text-align: center;">이더리움 이미지 다운로드</p>

> `ethereum/client-go` 는 Tag에 버전만 표기된 go-ethereum only 버전과 `alltools` 로 표기되어 다양한 도구들이 포함된 버전으로 나눌 수 있습니다. 도구 설치 여부만 다를 뿐, 1.13.x 버전이라면 어느 것을 사용해도 무방합니다. 

다운로드된 이미지는 Docker Desktop 의 `Images` 에서 확인할 수 있습니다. 

![downloaded_ethereum_image](/assets/images/2024-03-09-downloaded-ethereum-image.png){: .align-center}
<p style="text-align: center;">다운로드된 이더리움 이미지</p>

> Docker Hub 에서 제공되는 도커 이미지는 (대부분) Overview 페이즈에 이미지 사용법을 제공합니다. 앞으로 프라이빗 이더리움 구성에 필요한 정보는 Overview 를 기준으로 합니다. 다만, 개발속도와 문서화 간의 차이는 있을 수 있으므로, [이더리움 공식 문서](https://geth.ethereum.org/docs/fundamentals/private-network) 를 함께 살펴보기 바랍니다. 
![client_go_overview_docker_hub](/assets/images/2024-03-09-client-go-overview-docker-hub.png){: .align-center}
{: .notice--info}

#### client-go 이미지 실행하기

도커는 `docker run <이미지명 : Tag>`으로 실행됩니다. 이후 옵션들은 도커 실행에 필요한 정보를 넣을 수 있습니다. 

- 명령어 : `docker run --rm -it -p 0.0.0.0:8545:8545 -v "C:\Users\amana\OneDrive\바탕 화면\eth2":/home ethereum/client-go:alltools-v1.13.14`

> 옵션
- `--rm` : (optional) 도커가 종료되면 Stop 상태로 남게되고, 수동으로 Delete 를 해야 합니다. --rm 옵션은 도커 종료시 자동 삭제되도록 설정하는 옵션입니다. -it 옵션으로 접속 후 Console 을 종료하면, 이미지가 삭제됩니다.
- `-it` : (optional) 실행된 도커에 Console 로 여부를 지정합니다.
- `-p` : (optional) 접속하려는 IP 와 Port 를 지정합니다. 
- `-v` : (optional) 로컬의 임의의 디렉토리를 실행될 도커 내 디렉토리로 연결(Volume Mount) 을 지정합니다.

실행하면, 이미지에 연결된 프롬프트를 볼 수 있습니다. 

![execute_client_go_alltools](/assets/images/2024-03-09-execute-client-go-alltools.png){: .align-center}
<p style="text-align: center;">도커 이미지 실행</p>

#### (Optional) 도커에 접속하기

이전 명령어를 실행시 프롬프트가 나오지 않는다면, 다음 명령어로 접속합니다. 

- 명령어 : `docker exec -it <container id> sh`

> `container id`는 `docker ps -a`로 확인할 수 있습니다.
{: .notice--info}

### 노드 Minging 계정 생성하기 (공통)

노드별로 계정정보는 다르게 설정할 수 있습니다. 따라서, 각각 저장하기 위해 노드별 디렉토리를 생성한 후 계정을 생성/저장합니다. 

#### 노드별 디렉토리 생성하기

자신만의 노드별 디렉토리를 만듭니다.

- 명령어 : `mkdir bootnode node1`

> 이해하기 쉽도록 명시적으로 bootnode 라고 지정했을 뿐, 임의의 노드를 bootnode 로 사용해도 됩니다. 
{: .notice--info}

#### Mining 계정 생성하기

계정을 생성할 때, Password 를 반복 입력합니다. 

- 명령어 : `geth account new --datadir bootnode`, `geth account new --datadir node1`

![create_miner_account](/assets/images/2024-03-09-create-miner-account.png){: .align-center}
<p style="text-align: center;">노드별 Mining 계정 생성</p>

계정 생성이 모두 완료되면, 각 노드별 디렉토리에 계정을 저장하는 keystore 디렉토리가 생성되고 하위에 계정 생성시간을 이름으로 하는 계정별 Secret Key 를 볼 수 있습니다. 

![tree_view_keystore_each_node](/assets/images/2024-03-09-tree-view-keystore-each-node.png){: .align-center}
<p style="text-align: center;">계정 생성 완료시 파일 구성</p>

생성된 Mining 계정들은 genesis.json 에 기입할 예정이므로, 복사해 둡니다.

- bootnode miner 계정 : 0x6ddB80fB3faD94BE09bAaD2A5Cb8Df4cD419A9c9
- node1 miner 계정 : 0x7B65cD6274b033ca42bdDC0Fce3C5ee30667FBE1


### 프라이빗 이더리움 초기화하기 (공통)

genesis.json 를 만들고 genesis block 을 생성하는 것이 프라이빗 이더리움 초기화입니다.

#### Genesis.json 생성하기

`genesis` 단어에서 알 수 있듯, 이더리움 블록체인의 첫번째 블록을 생성하기 위해 필요한 정보를 설정하는 파일입니다.
genesis.json 을 생성할 때 입력되는 값 등 세부적인 내용은 이전 Post [프라이빗 이더리움 구성하기](https://keitechnote.github.io/dev/dev-install-private-ethereum/#genesisjson-%EC%83%9D%EC%84%B1) 를 참고하기 바랍니다.

```json
{
  "config": {
    "chainId": <Chain Id>,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "clique": {
      "period": 30,
      "epoch": 30000
    }
  },
  "difficulty": "1",
  "gasLimit": "8000000",
  "extradata" : "",
  "alloc": {}
}
```
JSON 데이터에서 `Chain Id`, `extradata` 와 `alloc` 부분을 추가합니다. 

`Chain Id` 는 생성할 프라이빗 이더리움을 대표하는 Id 입니다. 이를 통해 네트워크에서 이더리움 네트워크을 구분합니다.
즉, Chain Id 를 1로 설정하면, 이더리움 메인넷에 연결됩니다. (이 경우 메인넷 설정을 가져오게 됩니다.)

`extradata`는 블록 생성자 주소로 "0x"를 제외하고 32Bytes (64글자) 간격으로 블록 생성자 주소를 기입합니다. 각 주소간에는 32Bytes 의 "0"으로 채웁니다. 즉, 생성자 주소1 과 생성자 주소2 가 있을 때, `0x<0 32Bytes><address1><0 32Bytes><address2><0 32Bytes>` 로 생성합니다. 

```json
(생략)
"extradata" : "0x00000000000000000000000000000000000000000000000000000000000000006ddB80fB3faD94BE09bAaD2A5Cb8Df4cD419A9c900000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000007B65cD6274b033ca42bdDC0Fce3C5ee30667FBE10000000000000000000000000000000000000000000000000000000000000000"
```

`alloc` 은 생성할 프라이빗 이더리움의 계정을 지정하고, 계정별 이더를 할당할 수 있습니다. 단위는 Wei 입니다. 
bootnode 계정을 등록합니다. 

```json
(생략)
"alloc" : {
    "0x6ddB80fB3faD94BE09bAaD2A5Cb8Df4cD419A9c9" : {
        "balance" : 3000000000000000000
    }
}
```
> 이더리움은 Mining 방식을 PoW 인 [Ethash](https://geth.ethereum.org/docs/fundamentals/private-network#ethash) 와 PoA 인 [Clique](https://geth.ethereum.org/docs/fundamentals/private-network#clique) 를 지원합니다. 현재 PoA 로 설정했으며, PoW 는 genesis.json 의 `config` 하단의 `clique : {}` 를 `ethash : {}`로, extradata 를 제거합니다. 
{: .notice--info}

#### 노드 초기화

- 명령어 : `geth init --datadir bootnode genesis.json`, `geth init --datadir node1 genesis.json`

![init_private_ethereum_node](/assets/images/2024-03-09-init-private-ethereum-node.png){: .align-center}
<p style="text-align: center;">노드 초기화</p>

### 프라이빗 이더리움 실행하기 (bootnode)

하나의 bootnode 를 먼저 실행한 후, 개별 노드들을 bootnode 에 연결합니다. 

#### bootnode enode 확인하기

노드를 초기화하면, nodekey 가 생성됩니다. 다른 노드들이 접속할 enode 를 nodekey 로 생성합니다. 

- 위치 : `/home/bootnode/geth/`
- 명령어 : `bootnode -nodekey nodekey -verbosity 4`

![generate_bootnode_enode](/assets/images/2024-03-09-generate-bootnode-enode.png){: .align-center}
<p style="text-align: center;">bootnode 의 enode</p>

#### bootnode 실행하기

- 명령어 : `geth --datadir bootnode --port 30305 --bootnodes enode://24b660747874bd0daf0980eec5e729660480a0ea05864f30c286d32e0bdfa0f9e8b9f0d1cd724a13e61ccc0488850e72675cc162bdad4d84f36edfdb5a8371d6@127.0.0.1:0?discport=30301 --networkid 15 --unlock 0x6ddB80fB3faD94BE09bAaD2A5Cb8Df4cD419A9c9 --password bootnode/password.txt --http --http.addr 0.0.0.0 --http.port 8545 --http.corsdomain "*" --http.vhosts "*" --allow-insecure-unlock --mine --miner.etherbase 0x6ddB80fB3faD94BE09bAaD2A5Cb8Df4cD419A9c9`

![start_bootnode](/assets/images/2024-03-09-start-bootnode.png){: .align-center}
<p style="text-align: center;">bootnode 실행</p>

> 옵션
- `--datadir` : 노드 데이터와 keystore 를 저장하는 디렉토리 경로
- `--port` : 노드 Port
- `--bootnodes` : bootnode 들의 enode 목록 (구분자 : ",")
- `--networkid` : Chain Id
- `--unlock` : 계정내 Balance 를 사용할 수 있도록, 잠금해제하는 계정 목록 (구분자 : ",")
- `--http` : HTTP-RPC enable 
- `--http.addr` : HTTP-RPC IP
- `--http.port` : HTTP-RPC Port
- `--http.corsdomain` : Cross Origin Request 를 허용할 도메인 목록
- `--http.vhosts` : 접속을 허용할 가상 호스트 목록
- `--allow-insecure-unlock` : 계정 잠금 해제 허용
- `--mine` : Mining enable
- `--miner.etherbase` : Miner Address
{: .notice--info}

### 프라이빗 이더리움 실행하기 (node1)

bootnode 처럼 별도 node1용 도커를 실행해, node1용 도커에서 node1 을 실행합니다. 

#### 노드용 도커 실행하기

하나의 PC 에서 2개의 도커를 실행하므로 연결 Port 를 `8546` 으로 변경합니다. 

- 명령어 : `docker run --rm -it -p 0.0.0.0:8546:8546 -v "C:\Users\amana\OneDrive\바탕 화면\eth2":/home ethereum/client-go:alltools-v1.13.14`

#### 개별 노드 실행하기

- 명령어 : `geth --datadir node1 --port 30306 --bootnodes enode://24b660747874bd0daf0980eec5e729660480a0ea05864f30c286d32e0bdfa0f9e8b9f0d1cd724a13e61ccc0488850e72675cc162bdad4d84f36edfdb5a8371d6@127.0.0.1:0?discport=30301 --networkid 15 --unlock 0x7B65cD6274b033ca42bdDC0Fce3C5ee30667FBE1 --password node1/password.txt --authrpc.port 8552`

![start_node1](/assets/images/2024-03-09-start-node1.png){: .align-center}
<p style="text-align: center;">node1 실행</p>


### 참고

* [Clique signing](https://geth.ethereum.org/docs/tools/clef/clique-signing#main-content)
* [Setup a Private Blockchain using Geth and Docker](https://coinsbench.com/setup-a-private-blockchain-using-geth-and-docker-6060a1169e6f)
* [How to build an ethereum private blockchain network using geth and docker](https://hemantkgupta.medium.com/how-to-build-an-ethereum-private-blockchain-network-using-geth-and-docker-41f2ce8d6f6e)