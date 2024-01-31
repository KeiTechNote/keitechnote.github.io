---
layout: single
title: Ganache 설치하기
date: 2024-01-31 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum, EVM, Ganache, Ganache-cli]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

Ganache 는 가상 Ethereum 운영 환경을 구성할 수 있도록 Truffle 에서 제공하는 프로그램으로, TruffleSuite 를 설치하면 함께 설치됩니다 
Ganache 는 GUI 버전과 CLI 버전을 모두 지원합니다. 자신의 취향에 맞게 선택해 설치하면 됩니다. 
GUI 보단 CLI 설치가 낯설고 익숙하지 않지만, 개발환경에서는 GUI 보다 CLI 가 편리하다고 생각되어 CLI 버전을 기준으로 설명합니다. 

### Ganache-CLI 설치

Ganache-CLI 를 설치하는 방법은 TruffleSuite 와 같이 터미널에서 npm 명령어를 사용합니다.
( 만약, NodeJS 가 설치되어 있지 않다면, [NodeJS 다운로드](https://keitechnote.github.io/dev/dev-install-trufflesuite/#nodejs-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C)를 참고하기 바랍니다.)

- 명령어 : ```npm install -g ganache-cli```

![Ganache-Cli 설치](/assets/images/2024-01-31-ganache_cli_install.png){: .align-center}
<p style="text-align: center;">Ganache-Cli 설치</p>

설치가 정상적으로 완료되었다면, 터미널에서 실행해 다음과 같은 화면이 나온다면, 설치가 완료되었습니다. 

- 명령어 : ```ganache-cli```

![Ganache-Cli 실행](/assets/images/2024-01-31-ganache_cli.png){: .align-center}
<p style="text-align: center;">Ganache-Cli 실행</p>

> 공식 ReadMe 파일을 살펴보면, `ganache` 명령어로 실행하도록 되어 있으나 ganache 는 GUI 버전 실행 명령어이므로 CLI 버전은 `ganache-cli`명령어로 실행해야합니다.  
