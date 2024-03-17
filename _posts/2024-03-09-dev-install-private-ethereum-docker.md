---
layout: single
title: 도커에 프라이빗 이더리움 구성하기
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
그 과정을 살펴보면, geth 를 사용해 로컬의 지정한 디렉토리에 초기화하고, 도커로 실행되므로, 로컬에 구성하는 것과 크게 다르지 않습니다. 
다만, 이전에는 이더리움 1.0 기술이 주를 이루는 1.10.26 버전을 사용하고, 단일 노드만 구성 했다면, 이번엔 최신버전을 사용하고, 멀티 노드를 구성합니다. 

> 1.10.26버전도 멀티 노드를 구성할 수 있으나, 앞으로 사용할 최신버전과 멀티 노드 구성방식에 차이가 있습니다. 오해없길 바랍니다.
{: .notice--info}

### 사전 설치 프로그램

도커에 구성하기 위해, Docker Desktop 설치를 선행합니다. 설치에 필요한 과정은 다음 Post 를 참고하기 바랍니다.

- [Docker Desktop(Kubernetes) 설치하기](https://keitechnote.github.io/dev/dev-install-docker-desktop/)

### 도커 환경 구성하기

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

도커에서는 이미지 다운로드를 `Pull` 이라고 합니다. 이더리움 이미지를 다운로드 합니다. 버전을 명시하지 않는다면, 항상 최신 버전이 다운로드 됩니다. 

- 명령어 : `docker pull ethereum/client-go:alltools-latest`

![pull_ethereum_image](/assets/images/2024-03-09-pull-ethereum-image.png){: .align-center}
<p style="text-align: center;">이더리움 이미지 다운로드</p>

> `ethereum/client-go` 버전은 다양한 툴이 포함되지 않은 버전입니다. 여러 노드들을 연결하기 위해선 `bootnode` 가 필요하므로, 이미지 Tag 에 `alltools` 가 포함된 버전을 다운로드 합니다. 

다운로드된 이미지는 Docker Desktop 의 `Images` 에서 확인할 수 있습니다. 

![downloaded_ethereum_image](/assets/images/2024-03-09-downloaded-ethereum-image.png){: .align-center}
<p style="text-align: center;">다운로드된 이더리움 이미지</p>

> Docker Hub 에서 제공되는 도커 이미지는 (대부분) Overview 페이즈에 이미지 사용법을 제공합니다. 앞으로 프라이빗 이더리움 구성에 필요한 정보는 Overview 를 기준으로 합니다. 다만, 개발속도와 문서화 간의 차이는 있을 수 있으므로, [이더리움 공식 문서](https://geth.ethereum.org/docs/fundamentals/private-network) 를 함께 살펴보기 바랍니다. 
![client_go_overview_docker_hub](/assets/images/2024-03-09-client-go-overview-docker-hub.png){: .align-center}
{: .notice--info}

### 

`bootnode` 등 일부 정보는 geth 이 실행된 상태에서 동작합니다. 따라서, 필수적으로 1번은 geth 를 실행해야 합니다. 

#### 도커 이미지 실행하기

도커는 `docker run <이미지명 : Tag>`으로 실행됩니다. 이후 옵션들은 도커 실행에 필요한 정보를 넣을 수 있습니다. 

- 명령어 : `docker run --rm -it -p 0.0.0.0:8545:8545 -v "C:\Users\amana\OneDrive\바탕 화면\eth2":/home ethereum/client-go:alltools-stable`

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

#### bootnode 키 생성하기

모든 나무가지는 가장 큰 줄기에서 뻗어나가듯 이더리움 네트워크도 여러갈래로 나뉘어진 나무와 같습니다. 
따라서, 가장 큰 줄기가 되는 기준이 있으며, 그 기준이 `bootnode` (또는 `bootstrap node`) 입니다. 
여러 노드들을 연결하는 프라이빗 이더리움을 구성하기 위해, 기준이 되는 bootnode 를 설정합니다. 

bootnode 설정은 bootnode 키를 생성해 지정함으로써 완료됩니다. 
Home 디렉토리 (이미지 실행시 Mount 했던 디렉토리) 로 이동해 bootnode 키를 생성합니다. 

- 명령어 : `bootnode -genkey boot.key`

![create_boot_node_key](/assets/images/2024-03-09-create-boot-node-key.png){: .align-center}
<p style="text-align: center;">bootnode 키 생성</p>









### 프라이빗 이더리움 초기화

초기화는 이더리움을 Docker 로 실행한 후 Mining 계정을 생성합니다. 





### 참고

* [Setup a Private Blockchain using Geth and Docker](https://coinsbench.com/setup-a-private-blockchain-using-geth-and-docker-6060a1169e6f)
* [How to build an ethereum private blockchain network using geth and docker](https://hemantkgupta.medium.com/how-to-build-an-ethereum-private-blockchain-network-using-geth-and-docker-41f2ce8d6f6e)