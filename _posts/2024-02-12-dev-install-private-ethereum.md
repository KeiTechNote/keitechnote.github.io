---
layout: single
title: Private Ethereum 구성하기
date: 2024-02-13 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum, Private Ethereum, 프라이빗 이더리움, go-ethereum, geth]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

`go-ethereum` (이하 geth) 를 사용하면, 나만의 프라이빗 이더리움 네트워크를 구성할 수 있습니다. 
이 과정은 이더리움 메인넷을 만드는 과정과 크게 다르지 않습니다. 따라서, 프라이빗 이더리움 네트워크를 구성하면서 이더리움에 이해도를 높을 수 있습니다. 

### 사전 프로그램 설치

프라이빗 네트워크를 구성하기 위해 노드를 구성할 수 있는 geth 를 다운받아 설치해야 합니다. 설치에 필요한 과정은 다음 Post 를 참고하기 바랍니다.

* [go-ethereum 설치하기](https://keitechnote.github.io/dev/dev-install-go-ethereum/)

### Genesis.json 생성

이더리움은 첫번째 블록을 `제네시스 블록` 이라 합니다. "제네시스" 가 "기원"이란 뜻처럼 최초를 의미합니다.
이를 바탕으로 생각해 보면, genesis.json 은 제네시스를 위한 정보를 담고 있음을 유추해 볼 수 있습니다. 
프라이빗 네트워크 또한 genesis.json 을 사용해 제네시스 블록을 시작으로 이더리움 네트워크를 구성하게 됩니다. 

원하는 위치에 파일명 `genesis.json` 를 생성합니다. 본 Post 에서는 "eth" 디렉토리를 만든 후 eth 디렉토리 내 genesis.json 을 만듭니다.

- 파일 위치 : ~\eth\genesis.json

genesis.json 에는 다양한 정보가 있습니다. 그 중 기본적으로 다음의 내용이 담겨져 있습니다. 

> **설정값**
- `config` : 이더리움 네트워크 동작에 필요한 추가 설정 정보 (예: 이더리움 업데이트 적용 정보)
    - `chainId` : 이더리움 계열 네트워크를 식별하기 위한 숫자 (예: 이더리움 메인넷 : 1)
- `difficulty` : 채굴 난이도 (0x 로 시작되는 16진수 문자)
- `gasLimit` : 소모할 수 있는 최대 가스량 (0x 로 시작되는 16진수 문자)
- `alloc` : 계정 목록과 계정별 이더량

```json
{  
	"config": {
	    "chainId": "이더리움 네트워크 번호",
	},
	"difficulty": "",
	"gasLimit": "",
	"alloc": {}
}
```
이를 바탕으로 다음의 genesis.json 으로 프라이빗 네트워크를 구성합니다. 

```json
{  
	"config": {
	    "chainId": 15,
	    "homesteadBlock": 0,
	    "eip150Block": 0,
	    "eip155Block": 0,
		"eip158Block": 0
	},
	"difficulty": "0x20000",
	"gasLimit": "0x8000000",
	"alloc": {}
}
```
앞서 설명하지 않았던 다양한 값이 추가되어 있습니다. 

> **설정값**
- `homesteadBlock` : 프라이빗 이더리움의 경우, 최초 실행되는 블록 번호 또는 하드포크된 경우 하드포킹된 이후 시작되는 블록 번호 
- `eip150Block` / `eip155Block` / `eip158Block` : 이더리움 개선 프로토콜 중 특정 번호로 지정된 정책을 적용하는 블록 번호


동일 네트워크내 `chainId` 가 같을 경우, 충돌이 발생해 동작되지 않거나, 해당 이더리움 네트워크에 연결을 시도하게 됩니다. (이 특성을 이용해 여러 노드가 연결되는 네트워크를 구성하게 됩니다.) 
geth 실행시 옵션을 통해 다른 노드가 연결되지 않도록 제어할 수 있지만, 기본적으로 chainId 는 충돌되지 않도록 `고유 숫자`를 기입하기 바랍니다.
{: .notice--danger} 

### 프라이빗 이더리움 초기화

CLI 로 genesis.json 이 있는 위치로 이동해 다음의 명령어를 실행합니다. 

- 명령어 : `geth init --datadir .\chaindata .\genesis.json`

> **설정값**
- `datadir` : 프라이빗 이더리움 데이터 (블록, 계정, 엉클블록 등) 를 저장할 위치

`datadir`로 지정된 디렉토리가 생성되고, 추가로 다양한 파일과 디렉토리가 생성됩니다.

![tree_chaindata_dir](/assets/images/2024-02-12-tree-chaindata-dir.png){: .align-center}
<p style="text-align: center;">chaindata 디렉토리 구조</p>

명령어를 실행한 CLI 를 살펴보면 많은 정보를 알 수 있습니다. 

![init_private_ethereum](/assets/images/2024-02-12-init-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 초기화</p>

> **설정값 (공통)**
- Maximum peer count : 노드가 실행될 경우, 연결할 수 있는 최대 노드 수
- Set global gas cap : 이더리움 네트워크 전체에서 트랜잭션 처리량을 제한하기 위한 가스량을 제한
- Initializing the KZG Library : kzg 라이브러리 초기화

> **chaindata** 와 **lightchaindata**
- Defaulting to pebble as the backing database : 블록체인 클라이언트에서 사용되는 DB 로 pebble (경량화된 levelDB) 를 설정함
- Allocated cache and file handles : 캐시와 파일 핸들러 할당
- Opened ancient database : 엉클 블록을 위한 DB
- State schema set to default : 블록체인의 현재 상태를 설명하는 데이터 구조인 상태 스키마를 검증하는 방식을 "hash"로 설정함
- Writing custom genesis block : 제네시스 블록 생성 시작
- Successfully wrote genesis state : 제네시스 블록 생성 완료

`KZG` 라이브러리인 `Kate-Zaverucha-Goldberg polynomial commitment scheme` 은 분산시스템에서 사용되는 암호학적 기술로 커밋먼트를 생성하고 검증하는 과정에서 보안과 효율성을 제공하는 라이브러리입니다. 여기서는 go-ethereum 인 geth 로 초기화하므로, go 언어 kzg 인 gokzg 를 사용합니다.
{: .notice--info}


### 프라이빗 이더리움 실행

프라이빗 이더리움을 실행합니다.

- 명령어 : `geth --datadir .\chaindata`

![execute_private_ethereum](/assets/images/2024-02-12-execute-private-ethereum.png){: .align-center}
<p style="text-align: center;">프라이빗 이더리움 실행</p>

> chainId 를 고유숫자로 지정하지만, 간혹 동일한 chainId 가 네트워크상에 발견되어 Block 을 동기화하거나, 이웃 노드를 찾으려고 노력하기도 한다. 이 경우, eth 디렉토리와 genesis.json 을 제외한 모든 파일/디렉토리를 삭제하고, chainId 를 수정해 초기화 후 실행하는 방법과 옵션을 부여해 이웃 노드를 탐색하지 않도록 설정하는 방법이 있습니다. 이 중 후자의 경우 geth 실행시 `geth --datadir .\chaindata --nodiscover`로 실행합니다. 단, 다음과 같은 문구가 출력된다면, chainId 수정만 해결할 수 있습니다. 
![error_chainId](/assets/images/2024-02-12-error-private-ethereum-chainid.png){: .align-center}
{: .notice--info}

### 참고

chainId 는 이더리움 네트워크를 구분하기 위한 Id 입니다. 하지만 정확히는 이더리움의 EVM 호환성을 갖는 네트워크마다 chainId 를 등록하고 있습니다. 이를 확인하기 위해 다음의 사이트들을 참고하시기 바랍니다. 

- https://chainlist.org/
