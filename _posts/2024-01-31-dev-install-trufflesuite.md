---
layout: single
title: TruffleSuite 설치하기
date: 2024-01-31 05:30 +09:00
published: true
categories: [Dev]
tag: [Dev, 개발환경, BlockChain, Ethereum, EVM, Node, NodeJS, TruffleSuite, Truffle]
toc: true
author_profile: false
---

**[주의사항]** 
설치 환경은 윈도우 11 (한글) 을 기준으로 합니다. 따라서, 다운로드 등 일부분은 자신의 설치환경에 맞춰 진행하기 바랍니다.
{: .notice--danger} 

### 개요

TruffleSuite 는 Ethereum Virtual Machine 을 이용해 Ethereum 을 위한 개발 및 테스트 환경을 구성할 수 있는 프레임워크 입니다. 
[공식문서](https://trufflesuite.com/docs/truffle/how-to/install/) 를 살펴보면, 운영체제에 무관하며, NodeJS 를 사용하는 것을 알 수 있습니다. 

따라서, TruffleSuite 설치 전 NodeJS 를 설치합니다. 

### NodeJS 다운로드

NodeJS 다운로드 사이트에 접속합니다. 

- NodeJS : [다운로드](https://nodejs.org/ko/download)

붉은 색 박스로 표시한 윈도우용 NodeJS 를 클릭하면, NodeJS 설치 파일이 다운로드 됩니다. 

![NodeJS 다운로드](/assets/images/2024-01-31-nodejs_download.png){: .align-center }
<p style="text-align: center;">OS별 NodeJS 다운로드 페이지</p>

### NodeJS 설치

다운로드가 완료되면, 설치 파일을 실행하고 "다음" 버튼만 눌러서 설치하면 됩니다. 별도 선택할 부분은 없습니다. 
설치가 완료되면 버전 정보 출력을 통해 정상 실행여부를 확인합니다. 터미널을 실행한 후 다음의 명령어를 실행합니다. 명령어를 실행하는 위치는 어디든 상관없습니다. 

- 명령어 : ```node --version```

![NodeJS 설치](/assets/images/2024-01-31-nodejs_install.png){: .align-center }
<p style="text-align: center;">NodeJS 버전 확인</p>

### TruffleSuite 설치

TruffleSuite 는 NodeJS 에서 제공하는 `npm` (*N*ode *P*ackage *M*anager) 를 사용해 설치합니다. 터미널을 실행한 후 다음의 명령어를 실행합니다. 

- 명령어 : ```npm install -g truffle```

![Truffle 설치](/assets/images/2024-01-31-truffle_install.png){: .align-center }
<p style="text-align: center;">터미널 환경에서 TruffleSuite 설치</p>

> TruffleSuite 설치 명령어 중 `-g` 옵션은 `global` 의 약자입니다. npm 을 통해 설치하는 프로그램, 라이브러리들은 npm 명령어를 실행한 폴더에서만 사용하는 방식과 어디서든 사용할 수 있는 방식 두가지로 나뉜니다. truffle 의 경우 -g 옵셥을 통해 어디서든 사용할 수 있도록 했습니다. Prolog 에서 설명한 충돌 가능성에 대한 부분과 일맥상통하며 이는 Truffle을 사용하는 다양한 개발 환경을 어디서든 구성하고 사용할 수 있음을 의미합니다. 

설치가 정상적으로 완료되었다면, NodeJS 처럼 버전 정보 출력을 통해 정상 실행여부를 확인합니다. 

- 명령어 : ```truffle version```

![Truffle 버전](/assets/images/2024-01-31-truffle_version.png){: .align-center }
<p style="text-align: center;">TruffleSuite 버전 확인</p>

참고로 버전 확인시 Truffle 버전과 함께 Ganache, Solidity, Web3  정보도 함께 출력됩니다. 이는 TruffleSuite 설치시 함께 설치됐음을 의미합니다. 






