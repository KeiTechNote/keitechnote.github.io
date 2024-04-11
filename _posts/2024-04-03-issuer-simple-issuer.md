---
layout: single
title: DID, DID Document 만들기
date: 2024-04-03 05:30 +09:00
published: true
categories: [issuer]
tag: [Dev, SSI, DID, Python, FastAPI, 파이썬, Streamlit, 스트림릿, DID Document, ECDSA, DID Syntax, 구문, DID 구문, method-name, method-specific-id, DID-Core]
toc: true
author_profile: false
---

### 개요

Self-Soverign Identity (이하 SSI) 에서는 대상의 신원을 식별하기 위해 특별한 ID 를 사용합니다. 
이를 DID (Decentralized Identifier, 탈중앙화된 식별자) 라고하며 [DID-CORE](https://www.w3.org/TR/did-core/#dfn-decentralized-identifiers) 에서는 다음과 같이 정의하고 있습니다.  

```
A globally unique persistent identifier that does not require a centralized registration authority and is often generated and/or registered cryptographically. The generic format of a DID is defined in 3.1 DID Syntax. A specific DID scheme is defined in a DID method specification. Many—but not all—DID methods make use of distributed ledger technology (DLT) or some other form of decentralized network.

중앙 집중식 등록 기관이 필요하지 않으며 종종 암호화 방식으로 생성 및/또는 등록되는 전 세계적으로 고유한 영구 식별자입니다. DID의 일반적인 형식은 3.1 DID 구문에 정의되어 있습니다. 특정 DID 체계는 DID 방법 사양에 정의되어 있습니다. 전부는 아니지만 많은 DID 방법이 분산 원장 기술(DLT) 또는 다른 형태의 탈중앙화 네트워크를 사용합니다.
```

DID 가 생성되면, 검증할 수 있는 방법을 포함한 DID Document 를 만들고 이를 VDR (Verifiable Data Registry) 에 보관합니다. 
DID 를 검증하기 위해 사용되는 DID Document 를 [DID-CORE](https://www.w3.org/TR/did-core/#dfn-did-documents) 에서는 다음과 같이 정의하고 있습니다. 

```
A set of data describing the DID subject, including mechanisms, such as cryptographic public keys, that the DID subject or a DID delegate can use to authenticate itself and prove its association with the DID. A DID document might have one or more different representations as defined in 6. Representations or in the W3C DID Specification Registries.

DID 주체 또는 DID 위임자가 자신을 인증하고 DID와의 연관성을 증명하는 데 사용할 수 있는 암호화 공개 키와 같은 메커니즘을 포함하여 DID 주체를 설명하는 데이터 집합입니다. DID 문서는 6에 정의된 대로 하나 이상의 다른 표현을 가질 수 있습니다. 표현 또는 W3C DID 사양 레지스트리에 정의된 하나 이상의 다른 표현을 포함할 수 있습니다.
```

DID, DID Document 는 SSI 를 구성하기 위한 가장 기본적인 부분이며, 모든 구성요소들이 사용해야 하므로 가장 먼저 작성합니다. 

### DID 구문

DID 를 고유 식별자로 사용하기 위해선, DID 가 충돌되지 않도록 일정한 형식에 맞춰 작성해야 합니다. 
DID-CORE 에서는 [DID Syntax](https://www.w3.org/TR/did-core/#did-syntax) 로 정의하고 있습니다. 

![did_syntax_did_core](/assets/images/2024-04-03-did-syntax-did-core.png){: .align-center}
<p style="text-align: center;">DID Syntax</p>

즉, DID 는 `did:<method-name>:<method-specific-id>` 로 구성됩니다. 

> 구문
- `did:` : DID 임을 알 수 있는 스키마 또는 시그니처
- `method-name` : 메소드 이름으로 권한을 제공하는 단체나 서비스명 등을 사용합니다. 
- `method-specific-id` :  메소드별 ID, 메소드 이름을 기준으로 제공하려는 서비스나 권한을 구분하기 위해 사용되는 값입니다.

따라서 `<method-name>:<method-specific-id>` 으로 DID 의 권한을 식별 또는 부여, 관리합니다.

### DID 생성하기

DID 가 중복되지 않도록 생성하기 위해 UUID 등 사용할 수도 있지만, UUID 를 반복해 만드는 과정에서 동일한 값이 나올 가능성도 있기 때문에 수학적으로 충돌 가능성이 가장 낮은 방법으로 Public Key 를 이용해 만듭니다. 

> Public Key 를 이용해 ID 를 생성하는 이 방식은 이더리움의 주소 생성 방식과 유사합니다. 
![generate_ethereum_address](/assets/images/2024-04-03-generate-ethereum-address.png){: .align-center}

Public Key 를 생성하는 다양한 방법 중 ECC (Eliptic Curve Cryptography, 타원곡선암호) 를 사용합니다. 
이를 위해 필요 라이브러리를 설치합니다. 

- 명령어 : `pip install ecdsa`

![install_ecdsa](/assets/images/2024-04-03-install-ecdsa.png){: .align-center}
<p style="text-align: center;">ECDSA 라이브러리 설치</p>

DID 를 생성하는 과정은 두 단계로 구성됩니다 
1. Private Key 와 Public Key 를 생성합니다. 
2. Public Key 로 DID 를 생성합니다. 

```python
from ecdsa import SigningKey, SECP256k1
import base58

def generate_keypair():
    """
    Private Key, Public Key 쌍을 생성합니다. 
    """
    private_key = SigningKey.generate(curve=SECP256k1)
    public_key = private_key.verifying_key
    return private_key, public_key

def generate_did(method_name:str, public_key:bytes) -> str:
    """
    Public Key 로 DID 를 생성합니다. 
    """
    did_prefix = f"did:{method_name}:"
    did_suffix = base58.b58encode(public_key.hex().encode()).decode()
    return did_prefix + did_suffix
```

함수를 실행해 DID 를 생성하는 테스트 코드를 작성해 테스트 합니다.

```python
# 키쌍 생성
private_key, public_key = generate_keypair()
print(f"Private Key : {private_key.to_string().hex()}")
print(f"Public Key : {public_key.to_string().hex()}")

# DID 생성
did = generate_did("DoubleKei", public_key.to_string())
print(did)
```

![generate_did_test_code](/assets/images/2024-04-03-generate-did-test-code.png){: .align-center}
<p style="text-align: center;">키 쌍과 DID 생성 테스트</p>

> DID 의 `method-specific-id` 가 너무 길어 식별 및 관리가 쉽지 않다는 단점도 있습니다. 따라서, `method-name` 기준으로 충돌되지 않도록 UUID, Number, RBAC, Sequence 숫자 등을 사용하기도 합니다. 
{: .notice--info}

### DID Document 형식

생성된 DID 에 대한 DID Document 를 작성합니다. DID Document 는 DID 에 대한 권한, 용도 등을 포함한 설명서입니다. 
이를 위해 DID-CORE 문서에서는 설명에 필요한 세부적인 속성들을 [DID Document Properties](https://www.w3.org/TR/did-core/#did-document-properties) 에 정의하고 있습니다. 

![did_document_properties_did_core](/assets/images/2024-04-03-did-document-properties-did-core.png){: .align-center}
<p style="text-align: center;">DID Document Properties</p>

### DID Document 만들기

Properties 에서 Required 로 표기된 항목인 `id` 외에 문서에 표기되진 않았지만, DID 표준화 버전을 알 수 있도록 `@context` 에 버전 링크를 포함합니다. 따라서, 최소한의 모습은 다음과 같습니다. 

```python
did_document = {
    "@context": "https://www.w3.org/ns/did/v1",
    "id": did
}
```

DID Document 가 VDR 에 보관되고, Verifier 등 누군가가 이를 참조해 검증을 하려고 한다면, 이를 위한 정보도 포함되어야 합니다. 
우선 DID Document 가 DID 소유자가 만든 것이 맞는지 확인할 수 있어야 합니다. 이를 위해 DID Document 는 DID 생성전 만든 Private Key 로 서명하게 됩니다. 

> 서명이란 데이터를 해시하고, 해시된 값을 Private Key 로 암호화하고, 이 값을 서명한 데이터에 포함하는 행위입니다. 
{: .notice--info}

did_document 를 서명합니다. 

```python
# 1. 서명을 하기 위해, 데이터 형식을 변경합니다. 
import json
did_document_json = json.dumps(did_document, separators=(',', ':'), sort_keys=True).encode()

# 2. did_document 를 SHA256 으로 해싱 후 암호화합니다. 
import hashlib
signature = private_key.sign(did_document_json, hashfunc=hashlib.sha256)

# 3. 서명값의 형식을 Base64 로 변경합니다. 
signature_b64 = base64.b64encode(signature).decode()
```

서명값이 생성되었다면, 이 값을 did_document 에 추가합니다. 

```python
did_document.update({
    "proof" : {
        "type" : "EcdsaSecp256k1Signature2019",
        "created" : "2024-04-11T20:20:00",
        "verificationMethod": did + "#keys-1",
        "proofPurpose": "assertionMethod",
        "signatureValue": signature_b64
    }
})
```

> proof 매개변수
- `type` : 서명방식
- `created` : 서명한 시간
- `verificationMethod` : 서명을 검증하는데 사용되는 Public Key
- `proofPurpose` : 서명의 목적
- `signatureValue` : 서명값
{: .notice--info}

Proof 의 VerificationMethod 값은 서명 검증에 필요한 Public Key 를 의미합니다. 이 Public Key 는 DID Document 에 표함되어야 하므로 실제 최소한의 DID Document 구성은 다음과 같습니다. 

```python
did_document = {
    "@context": "https://www.w3.org/ns/did/v1",
    "id": did,
    "publicKey": [{
        "id": did + "#keys-1",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": did,
        "publicKeyHex": public_key.to_string().hex()
    }],
    "authentication": [{
        "type": "EcdsaSecp256k1SignatureAuthentication2019",
        "publicKey": did + "#keys-1"
    }]
}
```
서명까지 포함된 모습은 다음과 같습니다. 
```python
did_document = {
    "@context": "https://www.w3.org/ns/did/v1",
    "id": did,
    "publicKey": [{
        "id": did + "#keys-1",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": did,
        "publicKeyHex": public_key.to_string().hex()
    }],
    "authentication": [{
        "type": "EcdsaSecp256k1SignatureAuthentication2019",
        "publicKey": did + "#keys-1"
    }],
    "proof" : {
        "type" : "EcdsaSecp256k1Signature2019",
        "created" : "2024-04-11T20:20:00",
        "verificationMethod": did + "#keys-1",
        "proofPurpose": "assertionMethod",
        "signatureValue": signature_b64
    }
}
```

### DID Document 검증하기

Proof 값을 참조해 역순으로 구한 did_document 해시값과, did_document 를 직접 해싱한 값을 비교해 did_document 가 위변조되지 않았음을 검증합니다. 

```python
# Public Key 와 검증할 서명을 가져옵니다. 
publickey = did_document.get("publicKey", [])[0].get("publicKeyHex", "")
signature_b64 = did_document.get("proof", {}).get("signatureValue", "")
signature = base64.b64decode(signature_b64)

# 검증하기 위해, did_document 를 변환합니다. 
data_to_verify = json.dumps(did_document, separators=(',', ':'), sort_keys=True).encode()

# Public Key 형식을 변환합니다. 
public_key = VerifyingKey.from_string(bytes.fromhex(publickey))

# Public Key 로 서명을 검증합니다. 
public_key.verify(signature, data_to_verify, hashfunc=hashlib.sha256)
```


### 참고
* [W3C VC-DATA-MODEL](https://www.w3.org/TR/vc-data-model-2.0/)
* [W3C DID-CORE](https://www.w3.org/TR/did-core/)
* [W3C DID-SPEC-REGISTRIES](https://www.w3.org/TR/did-spec-registries/)