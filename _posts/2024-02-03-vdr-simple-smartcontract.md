---
layout: single
title: 간단한 VDR 스마트 컨트랙트 만들기
date: 2024-02-03 05:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, solidity, 블록체인, 이더리움, 스마트 컨트랙트, 솔리디티, SSI, DID]
toc: true
author_profile: false
---

### 개요

VDR 는 W3C 의 [DID-CORE][DID-CORE] 에서 다음과 같이 정의하고 있습니다. 

```
Verifiable data registries

In order to be resolvable to DID documents, DIDs are typically recorded on an underlying system or network 
of some kind. Regardless of the specific technology used, any such system that supports recording DIDs and 
returning data necessary to produce DID documents is called a verifiable data registry. Examples include 
distributed ledgers, decentralized file systems, databases of any kind, peer-to-peer networks, and other 
forms of trusted data storage.

DID document로 확인하기 위해선, DID는 어떤 종류의 시스템이나 네트워크에 저장되어야 합니다. 특정 기술에 관계없이, 
DID 를 저장하고 DID document를 생성하는데 필요한 데이터를 반환하는 시스템을 verifiable data registry라고 합니다. 
예를 들어, 분산 원장, 분산 파일 시스템, 모든 종류의 데이터 베이스, P2P 네트워크, 신뢰할 수 있는 데이터 스토리지가 있습니다.
```

즉, VDR 은 신뢰할 수 있어야하고 분산되어 있어야하며 탈중앙화를 만족해야 합니다. 이 조건을 만족할 수 있다면, 시스템이 어떤 종류이 건 문제되지 않습니다. 
그 동안 이 조건을 만족하는 시스템이 없지 않았습니다. MySQL 과 같은 SQL 형식의 RDBS 나 MongoDB 와 같은 NoSQL 형식의 BigData 처리 시스템등 다양한 형태의 DB 가 발전해 오면서 VDR 조건을 충족시킬 수 있었습니다.
그럼에도 불구하고 SSI가 블록체인이 공개된 이후 더욱 적극적으로 연구되고 있는지를 생각해보면, VDR 조건은 충족하나 이를 검증할 수 있는 방법이 명확하지 않습니다. 99.999% 된다고 설명되더라도 이는 이상적인 환경에서 구성된 경우, 또는 이를 홍보하는 기업이 생각하는 환경이 만족했을 때 가능하며, 결국 한 기업에 종속되는 중앙화로 회귀되는 등의 문제가 있습니다. 따라서, 현재는 이 모든 조건을 만족하는 블록체인 환경에서 구현하는 것이 고려되고 있습니다. 이러한 이유로 VDR 을 블록체인 환경에서 구현하며 많은 블록체인 네트워크 중 이더리움 기반으로 구성합니다. 

처음부터 이더리움 메인넷 또는 테스트넷을 기준으로 개발하면 실제 개발보다는 부수적인 문제(테스트 Gas Facet, 처리 속도 문제 등)에 너무 많은 시간을 소모하게 되므로 처음에 구성했던 Ganache-cli 환경을 이더리움 테스트넷을 대체하는 개발 환경으로 선택했습니다. 
다만, 스마트 컨트랙트를 개발해 본 경험이 없는 상태에서 개발, 컴파일, 테스트, 배포에 이르기까지 전 과정을 신경쓰는 건 솔리디티 개발과 함께 스마트 컨트랙트 개발의 또 하나의 진입 장벽이 됩니다. 
그래서, 본 Post 에서는 Remix IDE 환경에서 스마트 컨트랙트를 개발해 '개발'에만 신경쓸 수 있도록 합니다. 

### simpleVDR 스마트 컨트랙트 개발

아직 우린 VDR 이 SSI 생태계에 반드시 필요하지만 어떤 정보를 관리해야 하는지 알지 못합니다. 

> VDR 이 관리해야 하는 대상은 `DID document` (이하 DID 문서) 이며 DID 문서를 생성할 때 세부적인 부분을 살펴볼 예정입니다.

하지만 데이터를 저장하고 검색시 반환하는 기능은 필요하기 때문에 이러한 기능을 갖는 간단한 형태의 VDR 을 스마트 컨트랙트로 작성합니다. 
스마트 컨트랙트 제작과 관련없는 부분은 지금은 고민의 대상이 아니기 때문에 우선 Remix IDE 를 통해 개발하고 테스트합니다. 
[Remix IDE][Remix IDE] 에 접속해 아래의 코드를 붙여넣을 수 있는 솔리디티(확장자 .sol) 을 생성합니다. 

> Remix IDE 는 브라우저를 사용해 온라인에서 스마트 컨트랙트를 개발하고 배포, 기능 테스트를 할 수 있는 개발 도구입니다. 

![remix_ide_1](/assets/images/2024-02-03-vdr_step1_remix_ide_1.png){: .align-center}
<p style="text-align: center;">Remix IDE 에 솔디디티 파일 생성하기</p>

생성된 simple_vdr.sol 파일에 아래의 코드를 붙여 넣습니다. 

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.0;

contract simpleVDR {
    mapping(string => string) _mapVDR;

    function register(string memory _did, string memory _document) public {
        _mapVDR[_did] = _document;
    }

    function resolve(string memory _did) public view returns (string memory) {
        return _mapVDR[_did];
    }
}
```
![remix_ide_2](/assets/images/2024-02-03-vdr_step1_remix_ide_2.png){: .align-center}
<p style="text-align: center;">simple_vdr.sol 파일에 코드 붙여넣기</p>

코드를 붙여넣은 후 작성된 스마트 컨트랙트를 Remix IDE 에서 제공하는 가상의 이더리움 환경에 배포하기 위해선 `컴파일` 단계를 거쳐야 합니다. 컴파일은 솔리디티로 개발된 스마트 컨트랙트를 이더리움 환경에서 동작할 수 있도록 다른 언어로 변환하는 과정으로 `ABI` 와 `ByteCode` 이 생성됩니다. 

> `ABI` 는 Application Binary Interface 의 약자로 일반적으로 두개의 프로그램 사이에 위치하는 프로그램으로 함수, 매개변수 등의 정보를 정의한 파일이나 형식을 말합니다. 솔리디티에서는 스마트 컨트랙트로 개발된 함수를 JSON 형태로 표현하고 있어 이더리움에서 컨트랙트의 함수를 실행할 때 필요한 정보를 담고 있습니다.
> `ByteCode`는 솔리디티 코드가 이더리움에서 실행될 때, 이더리움이 이해할 수 있도록 기계어로 변환한 것으로 16진수의 Hex값 형태로 되어 있으며 스마트 컨트랙트 배포시 블록체인에 저장되는 정보입니다. 

### 컴파일

컴파일은 Remix IDE 의 왼쪽 아이콘 중 세 번째 위치하는 'Solidity Compiler' 창에서 진행합니다.

![remix_ide_3](/assets/images/2024-02-03-vdr_step1_remix_ide_3.png){: .align-center}
<p style="text-align: center;">컴파일하기</p>


일반적으로 Remix IDE 는 컴파일을 자동으로 수행하기 때문에 'Solidity Compiler' 아이콘처럼 녹색 체크가 이미 표기됩니다.
( 붉은 색의 숫자가 표기된다면 오류가 있는 것이므로 코드를 완전히 삭제 후 위의 코드를 다시 붙여넣기 합니다. )

앞서 컴파일은 솔리디티 코드를 ABI 와 ByteCode로 변환하는 과정이라고 설명했습니다. 따라서, 컴파일이 완료되면 다음과 같이 ABI 와 ByteCode를 살펴볼 수 있습니다. 

![remix_ide_4](/assets/images/2024-02-03-vdr_step1_remix_ide_4.png){: .align-center}
<p style="text-align: center;">컴파일이 완료된 후 ABI 와 ByteCode 보기</p>

### 배포

배포는 Remix IDE 가 제공하는 가상 이더리움 환경을 대상으로 배포합니다. Remix IDE 를 살펴보면 배포할 수 있는 다양한 환경을 제공합니다. 그 중 'Remix VM' 으로 표기된 환경이 가상 이더리움 환경입니다. 

![remix_ide_5](/assets/images/2024-02-03-vdr_step1_remix_ide_5.png){: .align-center}
<p style="text-align: center;">Remix IDE 에서 제공하는 가상 이더리움 환경</p>

본 Post 는 그 중에서 상하이 환경 (Remix VM (Shanghai)) 을 사용합니다. 하지만, Remix VM 이라면 어떤 것을 사용해도 상관없습니다. 
배포를 위해 `Deploy` 를 클릭 합니다. 배포하면 2가지 변화가 생깁니다. 
'1. console 화면에서 트랜잭션이 처리되는 모습'을 볼 수 있고, 스마트 컨트랙트 배포 결과 '2. 배포된 컨트랙트를 확인'할 수 있습니다. 

![remix_ide_6](/assets/images/2024-02-03-vdr_step1_remix_ide_6.png){: .align-center}
<p style="text-align: center;">Remix VM 에 스마트 컨트랙트 배포하기</p>

배포가 완료된 스마트 컨트랙트를 사용해 보겠습니다. 이를 위해 배포된 스마트 컨트랙트 코드를 살펴보면,

![remix_ide_7](/assets/images/2024-02-03-vdr_step1_remix_ide_7.png){: .align-center}
<p style="text-align: center;">배포된 솔리디티 코드 살펴보기</p>

- Line 2 : 솔리디티 버전정보로 동작하는 상위버전 또는 하위버전을 지정할 수 있습니다. 솔리디티는 하위 호환성을 보장하는 개발 언어가 아니므로 동작할 수 있는 솔리디티 버전을 명시해야 합니다.  
- Line 4 : 스마트 컨트랙트 이름으로 예제는 'simpleVDR'로 지정했습니다. 
- Line 7 ~ 9 : 첫 번째 'register' 함수를 정의한 부분입니다. `_did` 와 `_document` 를 매개변수로 받도록 개발했습니다. 전달된 변수 중 _did 를 키값으로 _document 를 저장할 수 있도록 `mapping` (Line 5) 을 사용했습니다. 
- Line 11 ~ 13 : 두 번째 'resolve' 함수를 정의한 부분입니다. register 함수에서 저장한 `_document` 를 `_did` 로 찾아서 반환합니다. 이를 위해 매개변수는 _did 를 받도록 개발되었습니다. 

> `mapping` 은 다른 언어에서 매핑 또는 딕셔너리, Key-Value 방식으로 불리는 데이터 저장 중 하나입니다. 딕셔너리(사전) 라는 단어의 뜻을 알 수 있듯, Key 를 기준으로 Value 를 찾을 수 있도록 저장하는 형태를 의미합니다. 위의 경우, register 함수에서는 _did 를 Key 로 _document를 저장하고, resolve 함수에서는 _did 를 Key 로 _document 를 찾아 반환합니다. 이렇게 저장하기 위해서 Line 5처럼 `mapping` 키워드를 선언한 후 Key 값의 자료형과 Value 값의 자료형을 지정하고, (_mapVDR 처럼) mapping 변수명을 지정하면 됩니다. 

![remix_ide_8](/assets/images/2024-02-03-vdr_step1_remix_ide_8.png){: .align-center}
<p style="text-align: center;">배포된 컨트랙트 함수 실행해 보기</p>

### 동작 테스트

개발이 완료되었다면 동작을 테스트합니다. resolve 함수는 _did 값에 맞춰 저장된 값을 불러오기 때문에 우선 저장이 선행되어야 합니다. 
따라서, register 함수를 먼저 실행합니다. 

이전 그림의 (붉은색 박스처럼) register 함수는 _did, _document 매개변수를 전달받아야 하므로 원하는 값(자료형이 string 이므로 문자) 을 입력합니다. 본 Post 에서는 _did 로 'abc' 를, _document 로 'dkei.github.io/blog'를 입력했습니다. 'transact' 를 클릭하면 오른쪽 화면(console 화면)에 'register' 호출 트랜잭션이 처리되는 것을 확인할 수 있습니다. 

(노란색 박스처럼) resolve 함수는 _did 매개변수를 전달받아야 하므로 원하는 값(자료형이 string 이므로 문자)을 입력합니다. 본 Post 에서는 _did 로 'abc'를 입력했습니다. 'resolve' 를 클릭하면 console 화면에 'resolve' Call 이 처리되고 그 결과가 resolve 함수 아래에 표기되는 것을 볼 수 있습니다. 


### 참고
* [W3C VC-DATA-MODEL](https://www.w3.org/TR/vc-data-model-2.0/)
* [W3C DID-CORE](https://www.w3.org/TR/did-core/)
* [W3C DID-SPEC-REGISTRIES](https://www.w3.org/TR/did-spec-registries/)


[step1_init]: https://keitechnote.github.io/blog/posts/vdr-step1-init/
[VC-DATA-MODEL]: https://www.w3.org/TR/vc-data-model-2.0/
[DID-CORE]: https://www.w3.org/TR/did-core/
[Remix IDE]: https://remix.ethereum.org/
