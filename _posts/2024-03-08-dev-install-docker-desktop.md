---
layout: single
title: Docker Desktop (Kubernetes) 설치하기
date: 2024-03-08 00:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, 도커, 컨테이너, 쿠버네티스, Docker, Container, Kubernetes]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

로컬 PC 에서 개발을 진행하다보면, DB, NodeJS, Python, 서버 등 다양한 프로그램을 설치합니다. 하지만 하나의 목적을 위해 설치한 프로그램들이 상호 버전간 호환성 등의 문제로 연결이 원활하지 않거나, 잘 사용하고 있던 프로그램의 버전을 올려야 하는 경우, 동작하지 않을 것에 대한 걱정으로 쉽게 변경하게 못하는 등의 문제가 있습니다. 

이러한 다양한 환경을 도커로 만들면, 쉽게 구성하고 테스트하고 관리할 수 있어 매우 편리합니다. 

### 다운로드

도커 데스크톱 사이트에 접속해 OS 별 설치파일을 다운로드 합니다. 

- Docker Desktop : [다운로드](https://www.docker.com/products/docker-desktop/)

![download_docker_desktop](/assets/images/2024-03-08-download-docker-desktop.png){: .align-center}
<p style="text-align: center;">Docker Desktop 다운로드</p>

### 설치하기

보통의 프로그램 설치 방식과 동일하게 설치합니다. 

![install_docker_desktop](/assets/images/2024-03-08-install-docker-desktop.png){: .align-center}
<p style="text-align: center;">Docker Desktop 설치</p>

> 본 Post 가 게시된 일 기준 현재 지원되는 최신 버전은 (윈도우 경우) 4.28.0입니다. 다만, 설치본은 4.27.2 이므로 설치 후 별도 업데이트를 진행하기 바랍니다.
- 위치 : 설정(톱니바퀴) > Software Updates 
{: .notice--info}

### Kubernetes 설정하기

Docker Desktop 설치만으로 바로 사용할 수 있지만 Docker 를 기반의 Kubernetes 를 사용하면, Kubernetes 에서 제공하는 많은 기능을 사용할 수 있습니다. 
Kubernetes 사용을 설정합니다. `Enable Kubernetes`를 체크한 후 `Apply & Restart` 를 선택하면 기능을 사용할 수 있습니다. 

- 위치 : 설정(톱니바퀴) > Kubernetes 

![enable_kubernetes_on_docker_desktop](/assets/images/2024-03-08-enable-kubernetes-on-docker-desktop.png){: .align-center}
<p style="text-align: center;">Kubernetes 설정</p>

### Docker 기본 명령어

Docker 는 설치한 GUI 를 사용할 수 있지만, CLI 를 사용해 다양한 동작을 할 수 있습니다. 그 중 가장 많이 사용하는 기능을 설명합니다. 

#### Docker 이미지 다운로드 하기

- 명령어 : `docker pull <이미지 이름>`

#### Docker 이미지 목록 확인하기

- 명령어 : `docker image ls`

#### Docker 이미지 실행하기

- 명령어 : `docker run <이미지 이름>`

> 실행 옵션
- `-d` : 데몬으로 실행
- `-it` : 터미널로 연결
- `-p` : 포트
- `-name` : 도커 닉네임
{: .notice--info} 

#### Docker 실행 목록 확인하기

- 명령어 : `docker ps -a`

#### Docker 에서 리눅스 명령(ls -al) 실행하기

- 명령어 : `docker exec -it <도커 ID> ls -al`

#### 실행중인 Docker 정지하기

- 명령어 : `docker stop <도커 ID>`

#### 정리한 Docker 종료하기

- 명령어 : `docker rm <도커 ID> or <도커 이름>`