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
Ganache 를 통해 이더리움 배포 전 점검은 완료되었습니다. 이제 로컬에 이더리움을 구성하고, 배포하겠습니다. 

### 사전 프로그램 설치

로컬에 이더리움 환경을 구성하기 위해, `go-ethereum (geth)` 를 사용합니다.
geth 는 Go 언어로 작성된 이더리움 노드로 쉽게 이더리움 네트워크에 접속해 동작되는 노드를 구성할 수 있습니다. 
따라서, 필요한 설치 과정은 다음 Post 를 참고하기 바랍니다.

- [이더리움 설치하기]()

