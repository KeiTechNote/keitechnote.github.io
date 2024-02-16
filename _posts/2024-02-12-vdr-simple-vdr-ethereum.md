---
layout: single
title: 프라이빗 이더리움에 간단한 VDR 스마트 컨트랙트 배포하기
date: 2024-02-12 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID, deploy]
toc: true
author_profile: false
---

### 개요

[Ganache 환경에 간단한 VDR 스마트 컨트랙트 배포하기][vdr-simple-vdr-truffle]를 통해 로컬 개발환경을 구성하고, 개발된 스마트 컨트랙트에 대한 테스트 및 Ganache 에 배포하는, 일련의 개발 사이클을 수행해 보았습니다. 이제 프라이빗 이더리움상에 배포해 보겠습니다. 
이를 위해선 프라이빗 이더리움 구성이 선행되어야 합니다. 

### 사전 프로그램 설치

프라이빗 이더리움은 로컬 PC 환경에 구성하고, 이를 위해 `go-ethereum (geth)`를 사용합니다. 
geth 는 Go 언어로 작성된 이더리움 클라이언트로 쉽게 이더리움 네트워크에 접속하거나 구동할 수 있습니다. 
필요한 설치 과정은 다음 Post 를 참고하기 바랍니다.

- [go-ethereum 설치하기](https://keitechnote.github.io/dev/dev-install-go-ethereum/)
- [프라이빗 이더리움 구성하기](https://keitechnote.github.io/dev/dev-install-private-ethereum/)
- [프라이빗 이더리움 셋업](https://keitechnote.github.io/dev/dev-setup-private-ethereum/)

### 배포 설정하기












[vdr-simple-vdr-truffle]: https://keitechnote.github.io/vdr/vdr-simple-vdr-truffle/