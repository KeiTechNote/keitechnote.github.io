---
layout: single
title: 스마트 컨트랙트 호출 과정
date: 2024-03-05 00:30 +09:00
published: true
categories: [Ethereum]
tag: [Dev, Blockchain, ethereum, smart contract, 블록체인, 이더리움, 스마트 컨트랙트, EVM, Ethereum Virtual Machine]
toc: true
author_profile: false
---

### 개요

스마트 컨트랙트를 개발하면 이더리움에 배포하고, 호출해 사용하는 과정이 무수히 반복됩니다. 
그 과정을 단순히 반복하기 보다는 컴파일 결과 생성된 ByteCode 가 무엇인지, Ethereum Virtual Machine (이하 EVM) 에서는 어떻게 동작하는지
이해한다면 스마트 컨트랙트에 대한 이해도를 높일 수 있으며, 어쩌면 발생할 수 있는 관련 오류를 쉽게 해결할 수 도 있습니다. 

### 컴파일부터 배포까지 과정 (요약 버전)

스마트 컨트랙트 개발이 완료되면, 컴파일을 통해 실행되는 Binary 인 ByteCode 와 ABI(Application Binary Interface) 가 생성됩니다. 
ByteCode 는 16진수 문자열로 Constructor() 와 같은 초기화 코드와 함수를 포함한 실행 코드로 구성됩니다. 
하지만, 16진수 문자열만으로는 함수가 무엇인지 변수 자료형은 무엇인지 알 수 없으므로, 컴파일시 함께 생성되는 ABI 를 활용합니다. 

> [스마트 컨트랙트용 API 서버 만들기](https://keitechnote.github.io/vdr/vdr-api-server/) 에서 JSON 파일에서 ABI 만 추출하는 이유가 여기에 있습니다.
{: .notice--info}

생성된 ByteCode 는 트랜잭션을 통해 이더리움 네트워크에 전파되고 각 노드는 컨트랙트를 실행하고 보관합니다. 

![from_compiling_to_deploy](/assets/images/2024-03-05-from-compiling-to-deploy.png){: .align-center}
<p style="text-align: center;">컴파일에서 배포까지 과정</p>

### Ethereum Virtual Machine (EVM)

스마트 컨트랙트가 배포되면, 컨트랙트는 수신받는 노드에서 실행됩니다. 이떄 실행되는 환경이 EVM 입니다.
PC로 비유하면, PC에서 실행되는 MS Excel, MS Word 와 같은 프로그램이며, 컨트랙트는 xls, doc 파일에 해당합니다. 
컴파일된 컨트랙트는 16진수로 작성된 Binary 이고, Binary를 분석하고 실행하는 역할을 EVM 에서 합니다. 
PC 에서 MS Excel 프로그램을 실행하기 위해 CPU, Memory 를 사용하듯, EVM도 실행하기 위해 리소스가 필요하고 이더리움 노드의 리소스를 사용합니다. 

![evm_architecture_layer](/assets/images/2024-03-05-evm-architecture-layer.png){: .align-center}
<p style="text-align: center;">EVM Architecture Layer
    (출처 : <a href="https://medium.com/@fabrisde167/getting-deep-into-evm-how-ethereum-works-backstage-ea70203e3124">Medium</a>)
</p>

하기 그림의 object는 Remix IDE 를 실행하면 포함된 Storage 컨트랙트의 ByteCode 입니다. 

![remix_ide_storage_contract_bytecode](/assets/images/2024-03-05-remix-ide-storage-contract-bytecode.png){: .align-center}
<p style="text-align: center;">Remix IDE 의 Storage 컨트랙트 ByteCode</p>

> 위 그림 중 opcodes 는 object 의 16진수 코드를 사람이 이해할 수 있는 어셈블리어로 변환한 코드입니다. 16진수에 매칭되는 어셈블리어는 이더리움에서 [EVM OpCodes](https://ethereum.org/ko/developers/docs/evm/opcodes/) 문서로 정의하고 있습니다. 이를 통해 ByteCode 를 어셈블리어로 변환해 주는 디스어셈블러를 만들 수도 있습니다.
{: .notice--info} 

### Contract Address 생성

컨트랙트를 처음 실행할 때, 노드는 ByteCode 중 초기화 코드만 실행합니다. 실행된 결과 (예: 변수 초기화 값, Owner Address 등) 를 State (상태) 라고 하며 블록의 머클트리(또는 상태트리)에 보관합니다. 
초기화 코드는 반드시 실행되고 그 결과는 블록에 저장되기 때문에 초기화 코드가 실행된 후 컨트랙트 코드 크기가 결정되고 가스비가 산정됩니다. 
Contract Address 도 이 시점에 생성됩니다. 생성된 Contract Address 는 노드 (Account)Storage 에 보관됩니다. 

![contract_creating](/assets/images/2024-03-05-contract-creating.png){: .align-center}
<p style="text-align: center;">Contract Address 생성</p>

각 노드에서 CA 를 생성하는 경우, 어디서든 CA 를 통해 컨트랙트를 실행시키기 위해선 CA 가 블록처럼 동기화되거나 모든 노드가 동일한 CA 가 생성되도록 해야 합니다. CA 의 경우, 트랜잭션에 포함된 트랜잭션 Sender 와 Nonce 를 이용해 CA 를 생성합니다. 

![compare_eoa_ca](/assets/images/2024-03-05-compare-eoa-ca.png){: .align-center}
<p style="text-align: center;">EOA 와 CA 생성 과정</p>

CA 가 생성되었으므로 컨트랙트를 호출 준비는 완료되었습니다. 

### ABI를 통한 함수 호출

컨트랙트 함수를 호출할 때, ABI 를 사용합니다. ABI 에는 함수명, 함수 매개변수 정보, 리턴값 등 모든 정보를 사람이 읽을 수 있는 텍스트 형식으로 저장하고 있습니다.  

![remix_ide_storage_contract_abi](/assets/images/2024-03-05-remix-ide-storage-contract-abi.png){: .align-center}
<p style="text-align: center;">Remix IDE 의 Storage 컨트랙트 ABI</p>

ABI 는 함수를 호출할 때, 매개변수 타입, 개수 등 확인하는데 사용됩니다. 
실제 함수 호출은 Function Hash를 사용합니다. 
Function Hash는 Getter() 가 함수 정보로 생성하는 Hash 로 함수 호출 트랜잭션의 입력값으로 사용됩니다. 

> Function Hash 만드는 로직
* 매개변수가 있는 함수 : `bytes4(keccak256("함수명(매개변수 자료형)"))`
* 매개변수가 없는 함수 : `bytes4(keccak256("함수명"))`

Storage 컨트랙트를 배포 후 매개변수 "10"을 넣고 Store() 를 호출합니다. 
"Compile Details" 에서 확인해 보면 Store() Function Hash는 `6057361d` 입니다. 

> Remix IDE Console 에서 Web3.js 라이브러리를 사용해 직접 계산할 수 있습니다.
- 계산 코드 : 
```
web3.eth.abi.encodeFunctionSignature({
    name: 'store',
    type: 'function',
    inputs: [{ type: 'uint256' }]
});
```
![remix_ide_calculate_function_hash](/assets/images/2024-03-05-remix-ide-calculate-function-hash.png){: .align-center}
{: .notice--info} 

Function Hash 는 Store() 를 호출한 트랜잭션의 Input 에 포함되어 있음을 알 수 있습니다. 

![remix_ide_storage_contract_call_store](/assets/images/2024-03-05-remix-ide-storage-contract-call-store.png){: .align-center}
<p style="text-align: center;">Remix IDE 의 Storage 컨트랙트 Store() 호출</p>

> 컨트랙트 ByteCode 나 트랜잭션 값등은 Big-Endian 방식으로 저장되므로, 앞에서부터 순서대로 읽습니다. 

### 컨트랙트 호출

CA 로 함수를 호출하면, 호출 요청을 받은 노드는 (Account)Storage 에서 컨트랙트 CA가 있는지 확인합니다. CA 가 확인되면, getCode()로 블록체인 내 저장된 실행 코드와 CA 로 블록체인에 저장된 이전 상태정보를 가져와 실행합니다. 
함수 실행이 완료되면 상태(State) 는 블록에 등록됩니다. 

![message_calling_contract](/assets/images/2024-03-05-message-calling-contract.png){: .align-center}
<p style="text-align: center;">컨트랙트 호출</p>

### 컨트랙트간 호출 

호출된 컨트랙트에서 다른 컨트랙트 함수나 값을 참조하는 경우가 있습니다. 이 경우 이더리움에서는 컨트랙트간 호출도 트랜잭션으로 분류하고 있으며, 가스비도 소모됩니다. 다만, EOA 에서 호출한 트랜잭션과 구분하기 위해 Internal Transaction 으로 명명하고 있습니다. 

![between_contract_and_contract_call](/assets/images/2024-03-05-between-contract-and-contract-call.png){: .align-center}
<p style="text-align: center;">컨트랙트간 호출</p>


### 참고
- [EVM Official Doc](https://ethereum.org/ko/developers/docs/evm/)
- [Merkle Patricia Trie](https://ethereum.org/ko/developers/docs/data-structures-and-encoding/patricia-merkle-trie/)
- [Deep into EVM](https://medium.com/@fabrisde167/getting-deep-into-evm-how-ethereum-works-backstage-ea70203e3124)