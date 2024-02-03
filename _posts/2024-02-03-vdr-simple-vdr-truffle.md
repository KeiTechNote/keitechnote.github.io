---
layout: single
title: Ganache 환경에 간단한 VDR 스마트 컨트랙트 배포하기
date: 2024-02-03 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID, Ganache, deploy]
toc: true
author_profile: false
---

### 개요

[간단한 VDR 스마트 컨트랙트 만들기][vdr-simple-smartcontract] 에서 가장 단순한 형태로 데이터를 저장하는 스마트 컨트랙트를 만들어보고, Remix IDE 에서 빌드 및 배포를 진행해 보았습니다. Remix IDE 는 빠르게 컨트랙트를 개발하고 동작 테스트를 하기에는 적합하지만 제품 수준의 스마트 컨트랙트를 개발하기에는 부족한 부분이 있습니다. 특히, 개발된 코드가 우리가 원하는 동작을 정상적으로 수행하는지 테스트하는 `단위 테스트 (unittest)`가 이에 해당합니다. 따라서, 제품 개발에 좀 더 맞는 개발 환경을 구성할 필요가 있습니다. 

### 사전 프로그램 설치

앞으로 개발하는 환경은 `VSCode`, `TruffleSuite`, `Ganache` 을 사용합니다. 
VSCode 는 Remix IDE 와 같은 개발도구입니다. 스마트 컨트랙트를 개발하고 빌드, 테스트를 진행할 때 사용됩니다. 
TruffleSuite 은 스마트 컨트랙트 개발에 필요한 기능을 제공하는 프레임워크 입니다. 앞서 언급한 단위 테스트등을 진행할 수 있도록 다양한 인터페이스를 제공하고 있습니다. 
Ganache 는 가상의 이더리움 환경을 제공하는 도구입니다. VSCode 에서 개발된 스마트 컨트랙트의 동작을 확인할 때, 가상 이더리움 환경에 배포해 테스트하게 됩니다. 
필요한 도구 설치 과정은 다음의 개별 Post 를 참고 하시기 바랍니다.

- [VSCode 설치하기](https://keitechnote.github.io/dev/dev-install-vscode/)
- [TruffleSuite 설치하기](https://keitechnote.github.io/dev/dev-install-trufflesuite/)
- [Ganache 설치하기](https://keitechnote.github.io/dev/dev-install-ganache/)

### Truffle 개발 환경 구성하기

개발환경을 구성할 기본 디렉토리를 만듭니다. 기본 디렉토리에서 터미널을 생성해 Truffle 템플릿을 설치합니다.

- 명령어 : `truffle unbox react`

![truffle_unbox_react_1](/assets/images/2024-02-03-vdr_truffle_unbox_react_1.png){: .align-center}
<p style="text-align: center;">Truffle React 설치</p>

> truffle 은 프로그램을 개발할 때 필요한 라이브러리와 도구를 묶은 `Box`를 제공합니다. 그 중 react 는 스마트 컨트랙트 개발, 컴파일, 테스트, 배포에 필요한 도구를 모아두었습니다. 따라서, 위 명령어를 통해 truffle 이 제공하는 react box 를 unbox 함으로써 설치됩니다. 
react 이외에도 [다양한 Box][truffle_boxes] 를 제공하니 참고하시 바랍니다.

기본 디렉토리를 VSCode로 열어보면 다음과 같은 구조를 볼 수 있습니다. 

![truffle_unbox_react_2](/assets/images/2024-02-03-vdr_truffle_unbox_react_2.png){: .align-center}
<p style="text-align: center;">React Box 구조</p>

전체 내용을 다루기 앞서 주로 다룰 폴더 및 파일을 소개합니다. 

- truffle/contracts : 개발된 스마트 컨트랙트가 위치할 폴더입니다. 
- truffle/migrations : 스마트 컨트랙트를 배포할 때, 배포 스크립트(JavaScript)가 위치할 폴더입니다. 
- truffle/test : 개발된 스마트 컨트랙트의 기능 테스트에 필요한 스크립트가 위치할 폴더입니다. 
- package.json : 사용되는 라이브러리 종류나 단축명령어를 스크립트로 지정하는 파일입니다. 
- truffle-config.js : 컴파일 프로그램 버전이나 배포되는 블록체인 네트워크 등을 지정하는 파일입니다. 

### simple_vdr 스마트 컨트랙트 생성하기 

[간단한 VDR 스마트 컨트랙트 만들기][vdr-simple-smartcontract] 에서 작성한 simpleVDR 을 새롭게 구성한 개발환경으로 옮기겠습니다. 
truffle/contracts 폴더에 동일한 이름의 simple_vdr.sol 파일을 만듭니다. 그 후 Remix IDE 에서 작성했던 코드를 복사해 붙여넣습니다. 

- 위치 : truffle/contracts/simple_vdr.sol

![simple_vdr_1](/assets/images/2024-02-03-vdr_truffle_simple_vdr_1.png){: .align-center}
<p style="text-align: center;">simpleVDR 파일 생성</p>

### simple_vdr 빌드/배포 스크립트 작성하기

Remix IDE 에서 해줬던 빌드와 배포를 개발 환경에 적용합니다. 스마트 컨트랙트를 빌드/배포할 때, 컨트랙트 간 의존성 등의 이유로 배포되는 순서가 중요합니다. Truffle 에서는 truffle/migrations 폴더에 빌드/배포를 위한 스크립트 파일을 작성할 때, 파일명에 숫자를 붙여 명시적으로 처리 순서를 기입하고 있습니다. 즉, truffle/migrations/1_deploy_simple_storage.js  처럼 1_~ 로 숫자로 시작하고, 숫자가 작은 순서부터 실행됩니다. 
따라서, 1_deploy_simple_storage.js 다음에 배포될 수 있도록 `2_deploy_simple_vdr.js` 에 작성합니다. 

- 위치 : truffle/migrations/2_deploy_simple_vdr.js

```javascript
const SimpleVDR = artifacts.require("simpleVDR");

module.exports = function (deployer) {
  deployer.deploy(SimpleVDR);
};
```

`artifacts.require("simpleVDR")` 에서 require는 라이브러리 등 파일을 읽어들이는 JavaScript 문법입니다. 여기서는 simple_vdr.sol 파일에서 simpleVDR 컨트랙트를 읽어들일 수 있도록 simpleVDR 컨트랙트명을 지정합니다. 읽어들인 컨트랙트는 SimpleVDR 변수에 저장됩니다. 
저장된 SimpleVDR 정보는 `deployer.deploy(SimpleVDR)`을 통해 배포(deploy)됩니다. 

> 배포를 위한 정보는 있지만 컴파일을 위한 정보를 별도로 작성하지 않았습니다. 하지만 truffle-config.json 파일에서 보듯 컴파일을 위한 기본정보는 설정되어 있으므로 별도 설정하지 않습니다. ![simple_vdr_2](/assets/images/2024-02-03-vdr_truffle_simple_vdr_2.png){: .align-center}

### 개발환경 접속하기

다음 명령어를 통해 컴파일/배포를 위해 truffle 개발 환경에 접속합니다. 

- 명령어 : `truffle develop`

![simple_vdr_3](/assets/images/2024-02-03-vdr_truffle_simple_vdr_3.png){: .align-center}
<p style="text-align: center;">truffle 개발 환경 접속</p>

Ganache-Cli 를 실행했을 때와 같은 화면을 볼 수 있습니다. truffle develop 명령어는 PC 에 구성된 개발환경에 접속하는 명령어입니다. 
현재 개발환경을 VSCode + TruffleSuite + Ganache-Cli 로 작성했으므로 Ganache-Cli 로 접속됩니다. 

![simple_vdr_4](/assets/images/2024-02-03-vdr_truffle_simple_vdr_4.png){: .align-center}
<p style="text-align: center;">truffle 지원 명령어</p>

### 배포하기

truffle/migrations 디렉토리에 1_deploy_simple_storage 와 2_deploy_simple_vdr 두개가 있으므로 배포시 두개가 배포 대상이 됩니다. 따라서, 컴파일된 파일이 2개가 표기됩니다. (1_deploy_simple_storage 는 관심대상이 아니므로 표기에서 제외했습니다. )
컴파일이 완료되면 `.\client\src\contracts` 에 컨트랙트명과 동일한 이름의 json 파일이 생성되며 Remix IDE 에서 확인했던 ABI 정보를 저장합니다. 

![simple_vdr_5](/assets/images/2024-02-03-vdr_truffle_simple_vdr_5.png){: .align-center}
<p style="text-align: center;">컴파일 화면(1)</p>

2_deploy_simple_vdr 을 통해 simple_vdr.sol 컨트랙트가 배포된 후 배포된 컨트랙트 주소나 소모된 Gas 비용등이 표기됩니다. 

![simple_vdr_6](/assets/images/2024-02-03-vdr_truffle_simple_vdr_6.png){: .align-center}
<p style="text-align: center;">컴파일 화면(2)</p>


[vdr-simple-smartcontract]: https://keitechnote.github.io/vdr/vdr-simple-smartcontract/
[truffle_boxes]: https://trufflesuite.com/boxes/