---
layout: single
title: Polygon VDR 컨트랙트 살펴보기
date: 2024-03-02 00:30 +09:00
published: true
categories: [vdr]
tag: [Dev, Blockchain, vdr, ethereum, smart contract, 블록체인, 이더리움, 스마트 컨트랙트, SSI, DID, Polygon, 폴리곤, EVM, PolygonDidRegistry]
toc: true
author_profile: false
---

### 개요

윈도우는 인텔/AMD CPU 에서 동작하고, 맥OS 는 맥용 CPU인 M1/M2에서 동작하는 것처럼, 이더리움의 스마트 컨트랙트는 이더리움 가상머신 (Ethereum Virtual Machine, 이하 EVM) 환경에서 동작합니다. 그런데 아발란체, 트론, 폴리곤 등 다수의 메인넷은 이더리움의 EVM 을 사용하고 있습니다. 
이로 인해, 이더리움과 EVM 호환성을 갖는 메인넷은 스마트 컨트랙트를 별다른 수정없이 배포할 수 있는 상호 호환성을 갖게됩니다. 
그 중 폴리곤은 EVM 호환성을 갖고 있으며, SSI 성격의 `Polygon ID` 프로젝트를 활발히 진행하고 있습니다. 
따라서, Polygon ID 에서 개발한 VDR 컨트랙트를 살펴 보겠습니다. 

### Polygon DID Register - 기본 사항

Polygon ID용 VDR은 Github 에 컨트랙트를 오픈하고 있습니다. 

- 위치 : `https://github.com/ayanworks/polygon-did-registry-contract/blob/main/contracts/PolygonDidRegistry.sol`

> Remix IDE 에서 동작 테스트를 할 수 있습니다.
{: .notice--info} 

#### 자료구조

스마트 컨트랙트에서 `struct` 와 `mapping` 는 데이터를 구조화하고 저장하는데 사용됩니다. 
struct 는 여러 변수를 하나의 데이터 타입으로 만들 때 사용하고, mapping 은 key-value 로 저장할 떄 사용합니다. 두 타입 모두 중복 구조를 가질 수 있습니다. 

> PolygonDidRegistry 컨트랙트에서는 `PolyDID` 데이터 타입을 만들고, controller, created, updated, didDoc 변수를 포함하도록 설계했습니다. 
PolyDID 데이터 타입은 다시 (address => PolyDID) 구조의 polyDIDs 를 만들 때 사용합니다. 이외에도 activeDIDs, activeAddress, resourceData, keysById 가 mapping 구조를 갖습니다. 
이 중 resourceData 는 (string => string) mapping 값을 value로 mapping 하는 중복 구조로 address 변수와 string 변수로 string 값을 찾는 이차원 배열입니다. 

```solidity
struct PolyDID {
    address controller;
    uint256 created;
    uint256 updated;
    string didDoc;
}
mapping(address => PolyDID) polyDIDs;
mapping(uint256 => address) activeDIDs;
mapping(address => uint256) activeAddress;
mapping(address => mapping(string => string)) resourceData;
mapping(address => string[]) private keysById;
```

#### 권한 제한

스마트 컨트랙트는 함수에 따라 지정된 소유자만 사용할 수 있도록 권한을 제한할 수 있습니다. 이를 위해 `modifier` 로 권한 검증 함수를 작성하고, 이후 권한 제한이 필요한 함수에 권한 검증 함수를 추가하는 방식입니다. 

> `onlyController()` 는 did (=_id) 가 트랜잭션 생성자 (=msg.sender) 인지 확인합니다. 
PolygonDidRegistry 컨트랙트에서는 `updateDIDDoc()` 과 `addResource()` 에서 onlyController() 로 권한 제한을 하고 있습니다. 

```solidity
modifier onlyController(address _id) {
    require(polyDIDs[_id].controller == msg.sender, "message sender is not the controller of the DID Doc");
    _;
}
```

> `onlyOwner()` 는 초기화시 설정된 스마트 컨트랙트 소유자가 트랜잭션 생성자 (=msg.sender) 인지 확인합니다. 
PolygonDidRegistry 컨트랙트에서는 `transferOwnership()` 에서 onlyOwner() 로 권한 제한을 하고 있습니다. 

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "message sender is not the owner");
    _;
}
```

#### 이벤트 함수

`event`는 함수 코드상에서 `emit <event 함수명>` 으로 실행하는 함수입니다. 
emit 으로 event 함수를 호출하면, 웹, 지갑 등 상위에서 이를 감지할 수 있도록 알림을 전달합니다. 이를 통해 상위에서 지갑의 계좌를 변경하거나 로그를 남기는 등 필요한 동작을 할 수 있습니다. 

> PolygonDidRegistry 컨트랙트에서는 총 4개의 event 함수를 정의하고 있습니다. 

```solidity
event DIDCreated(address id, string doc);
event DIDUpdated(address id, string doc);
event TransferOwnership(address newOwner);
event ResourceAdded(address _id, string _resourceId, string _resourcePayload);
```

#### 초기화 함수

스마트 컨트랙트 초기화는 `Constructor()`를 통해 배포시 단 1회 실행합니다. 
간혹 배포시 초기화가 불가능한 경우, 또는 업데이트가 발생하고 초기화가 여러 번 이뤄져야 하는 경우 initialize() 로 그 기능을 분리하고 별도 호출을 통해 실행됩니다. 

> PolygonDidRegistry 컨트랙트에서는 Constructor() 를 사용하지 않고 initialize() 만으로 초기화합니다.  초기화시 초기화 여부 확인 (initialized), 컨트랙트 소유자 (owner), 등록된 DID 수 (totalDIDs) 가 초기화 대상입니다.  
totalDIDs 는 등록된 DID 수를 반환 (`getTotalNumberOfDIDs()`함수) 하거나 activeDIDs 변수에서 DID 인덱스로 사용됩니다. 
private 변수는 변수를 선언한 컨트랙트 내에서만 사용/확인하기 위한 목적으로 설정됩니다. 하지만 private 변수이지만 동일 컨트랙트의 public 함수에서 설정하는 경우 "변수를 선언한 컨트랙트에서만 확인 가능"하다는 조건에 위배되지 않으므로 외부에서 public 함수 호출로 값을 변경할 수 있습니다. 따라서, private 변수에 대한 변경/사용/확인시에는 권한 제한을 설정하는 것이 안전합니다. 
![private_var_public_func](/assets/images/2024-03-02-private-var-public-func.png){: .align-center}

```solidity
uint256 totalDIDs;
address owner;
bool private initialized;

function initialize() public {
    require(!initialized, "Contract instance has already been initialized");
    initialized = true;
    owner = msg.sender;
    totalDIDs = 0;
}
```

#### 컨트랙트 소유자(owner) 관련 함수

컨트랙트 소유자만 사용해야 하는 중요 함수인 경우 `onlyOwner` 를 이용해 권한 제한을 할 수 있습니다. 

> PolygonDidRegistry 컨트랙트에서는 `transferOwnership()` 에서 권한 제한을 사용하며, `getOwner()`로 소유자를 확인할 수 있습니다. 

```solidity
function transferOwnership(address _newOwner) 
    public
    onlyOwner
    returns (string memory)
{
    if (owner != _newOwner) {
        owner = _newOwner;
        emit TransferOwnership(owner);
        return ("Ownership transferred successfully");
    } else {
        return ("Ownership cannot be transferred to the same account");
    }
}

function getOwner() 
    public 
    view 
    returns (address _owner) 
{
    return owner;
}
```

#### 기타 함수

그외 유틸리티 성격의 함수인 등록된 전체 DID 개수를 확인하는 함수가 있습니다. public 으로 선언되어 있으며, 단순 값을 반환할 뿐 값을 수정/설정하지 않으므로 `view` 로 설정합니다. 

```solidity
function getTotalNumberOfDIDs()
    public
    view
    returns (uint256 _totalDIDs)
{
    return (totalDIDs);
}
```

### Polygon DID Register - 주요 함수

VDR 핵심 기능인 register 와 resolver 를 PolygonDidRegistry 컨트랙트에서는 `createDID()` 와 `getDIDDoc()`, `getDIDDocByIndex()` 로 정의하고 있습니다. 
이외에도 simple_vdr 컨트랙트 작성시 DIDDoc 을 update하는 `updateDIDDoc()` 과 리소스 관련 함수 (`addResource()` `getResource()` `getAllResources()`) 가 추가되어 있습니다. 

> simple_vdr 컨트랙트는 [간단한 VDR 스마트 컨트랙트 만들기](https://keitechnote.github.io/vdr/vdr-simple-smartcontract/) 를 참고하기 바랍니다.
{: .notice--info}

#### createDID()

did 와 doc 을 등록합니다. 등록되는 정보들은 다음과 같은 구조를 갖습니다. 

![data_structure](/assets/images/2024-03-02-data-structure.png){: .align-center}
<p style="text-align: center;">데이터 구조</p>

등록이 완료되면, emit 으로 event 함수를 호출하고, 생성한 데이터를 전달합니다. 

```solidity
function createDID(address _id, string memory _doc)
    public
    returns (
        address controller,
        uint256 created,
        uint256 updated,
        string memory didDoc
    )
{
    polyDIDs[_id].controller = msg.sender;
    polyDIDs[_id].created = block.timestamp;
    polyDIDs[_id].updated = block.timestamp;
    polyDIDs[_id].didDoc = _doc;
    activeDIDs[totalDIDs] = msg.sender;
    activeAddress[_id] = totalDIDs;
    ++totalDIDs;
    emit DIDCreated(_id, _doc);
    return (
        polyDIDs[_id].controller,
        polyDIDs[_id].created,
        polyDIDs[_id].updated,
        polyDIDs[_id].didDoc
    );
}   
``` 

![exec_createdid_remix_ide](/assets/images/2024-03-02-execute-createdid-remix-ide.png){: .align-center}
<p style="text-align: center;">Remix IDE 에서 createDID() 실행</p>


#### updateDIDDoc()

등록되어 있던 doc 를 변경합니다. 변경은 doc 소유자만 가능합니다. (onlyController() 로 권한 제한)

```solidity
function updateDIDDoc(address _id, string memory _doc)
    public
    onlyController(_id)
    returns (
        address controller,
        uint256 created,
        uint256 updated,
        string memory didDoc
    )
{
    polyDIDs[_id].didDoc = _doc;
    polyDIDs[_id].updated = block.timestamp;
    emit DIDUpdated(_id, _doc);
    return (
        polyDIDs[_id].controller,
        polyDIDs[_id].created,
        polyDIDs[_id].updated,
        polyDIDs[_id].didDoc
    );
}
```

![onlycontroller_updatediddoc](/assets/images/2024-03-02-onlycontroller-updatediddoc.png){: .align-center}
<p style="text-align: center;">onlyController() 를 사용한 접근제어</p>

#### getDIDDoc() & getDIDDocByIndex()

VDR 주요 기능 중 resolver 에 해당하며 createDID() 로 등록된 데이터 중 did 로 doc 를 검색합니다. 

> PolygonDidRegistry 컨트랙트에서는 `getDIDDoc()` 은 did 로 doc 을 검색하고, `getDIDDocByIndex()` 는 index 로 doc 를 검색합니다. 이때, doc 으로 접근할 수 있는 대상(Resource)도 확인해 함께 전달합니다. 

```solidity
function getDIDDoc(address _id) public view returns (string memory, string[] memory) {
    string[] memory result = new string[](keysById[_id].length);

    for (uint256 i = 0; i < keysById[_id].length; i++) {
        result[i] = resourceData[_id][keysById[_id][i]];
    }
    return (polyDIDs[_id].didDoc, result);
}

function getDIDDocByIndex(uint256 _index)
    public
    view
    returns (string memory)
{
    return polyDIDs[activeDIDs[_index]].didDoc;
}
```

#### addResource() & getResource() & getAllResources()

Resource 는 did 로 연결할 수 있는 대상을 의미합니다. did 로 연결할 수 있는 대상은 doc 에 포함되어 있습니다. 

> PolygonoDidRegistry 컨트랙트에서 Resource 와 관련된 함수는 `addResource()` 와 `getResource()`, `getAllResource()` 가 있습니다. 
이때 `resourceData[_id]` 에 등록된 연결 대상인 resourceId 수를 알 수 없어, `keysById` 변수에 resourceId 수를 별도 관리합니다.

```solidity
function addResource(address _id, string memory _resourceId, string memory _resourcePayload) public onlyController(_id) returns (address, string memory, string memory) {
    resourceData[_id][_resourceId] = _resourcePayload;
    keysById[_id].push(_resourceId);
    emit ResourceAdded(_id, _resourceId, _resourcePayload);
    return (_id, _resourceId, _resourcePayload);
}

function getResource(address _id, string memory _resourceId) public view returns (string memory) {
    return resourceData[_id][_resourceId];
}

function getAllResources(address _id) public view returns (string[] memory) {
    string[] memory result = new string[](keysById[_id].length);

    for (uint256 i = 0; i < keysById[_id].length; i++) {
        result[i] = resourceData[_id][keysById[_id][i]];
    }

    return result;
}
```

### 참고

- Polygon ID Fundamentals : [Youtube](https://youtube.com/playlist?list=PLRD3rkREa7mLLJ6jfUTygXV1iK_AvsDdw&si=DrUJGFlb-aGV9f6_)
- Polygon ID Architecture : [Youtube](https://youtube.com/playlist?list=PLRD3rkREa7mLjDB1qL4KkCtob6kPJIvhS&si=6U7RuGa5AjW7_U3a)
- Polygon ID Inside : [Youtube](https://youtube.com/playlist?list=PLRD3rkREa7mIgx_RHZdin74vEb2A1Evek&si=2cRcrcWH4bkU14Wq) 
