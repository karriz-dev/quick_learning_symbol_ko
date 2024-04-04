# 2.개발 환경 구축하기

해당 섹션은 모든 문서를 어떻게 읽어야하는지에 대해 설명합니다.

## 2.1 언어

해당 퀵 러닝 자료의 코드는 `자바스크립트(JavaScript)`로 작성되었습니다.

### 표준 개발 키트 (Standard Development Kit, SDK)

현재 표준으로 제공 되는 것은 symbol-sdk-typescript-javascript 이며, 이외 커뮤니티에서 개발한 SDK도 제공되고 있습니다.

아래 제공 된 Symbol SDK는 최근(2024년 4월) 알파 버전의 3버전이 개발에 적합하지 않아 정식 릴리즈가 되기 전까지 긴급 패치를 하여 사용할 수 있도록 만든 `v2.0.5` 입니다. 추후 deprecated 될 수 있으니 참고해주시면 좋을 것 같습니다.

symbol-sdk-typescript-javascript (v2.0.5)  
https://github.com/symbol/symbol-sdk-typescript-javascript

#### 노트

> 현재(2024년 04월 기준) `symbol-sdk v3.1.0`은 알파 버전으로 릴리스되었으며, `v2.0.5`가 안정화 버전으로 제공되고 있습니다. 그리고 버전 3에서는 rxjs 의존적인 기능이 많이 제거되었으므로 REST API에 호출하는 방식으로 변경해야합니다.

### 참조

TypeScript and JavaScript 언어를 위한 심볼 SDK 문서 (v1.0.3)  
https://symbol.github.io/symbol-sdk-typescript-javascript/1.0.3

심볼 블록체인 코어(catapult)의 REST API 서버 엔드포인트 (v1.0.3)  
https://symbol.github.io/symbol-openapi/v1.0.3

## 2.2 샘플 소스 코드

### 변수 선언

이 문서에서는 콘솔에서 반복적으로 다시 작성하여 작동하는지 확인하기를 원하기 때문에 `const`로 변수를 선언하지 않습니다. 응용프로그램을 개발할 때에는 `const`로 변수를 선언하여 보안을 반드시 보장해야 합니다.

### 출력 값 확인

`console.log()`는 변수 내용을 출력합니다. 원하는 대로 출력 기능을 사용해보세요.  
출력은 `>` 다음에 표시됩니다. 샘플로 연습할 때는 이 부분을 제거하고 사용하세요.

### 동기와 비동기

다른 언어에 익숙한 개발자들은 비동기 처리를 쓰는 것이 불편할 수 있으므로, 특별한 이유가 없는 한 비동기 처리 없이 설명을 작성합니다.

### 계좌 (Account)

#### 철수

해당 매뉴얼에서는 철수라는 계정에 초점을 맞춥니다. 다음 보게 될 3장에서 부터 철수 계정을 계속 사용할 예정입니다. XYM을 충분히 해당 계정에 전송한 상태에서 본 설명서를 읽어주세요.

#### 영희

영희는 해당 챕터에서 요구하는 대로 철수와의 거래를 위한 계정으로 생성됩니다. 민수와 같은 또 다른 계좌는 이후 [다중서명](./09_multisig.md) 챕터에서 사용됩니다.

### 수수료

이 문서에서 트랜잭션의 거래 수수료는 `µXYM` 단위를 사용하며, 해당 퀵 러닝 자료에서는 수수료 배율을 `µXYM` 100으로 기본 설정 합니다.

#### 단위 표현

1 `µXYM` = 0.000001 `XYM`

```js
// 트랜잭션 수수료 예시 (챕터 4 부분에서 다시한번 나올 예정)
let transactionSize = 1000; // 트랜잭션의 크기        (1,000 bytes)
let transactionFee = 100; // 트랜잭션의 수수료 배율     (x100)

let transactionMaxFee = 100000; // 총 최대 수수료     (uXYM 100,000 = 0.1 XYM)
```

## 2.3 사전 준비

노드 목록에서 새로운 페이지를 오픈합니다(예: 크롬 브라우저). 해당 매뉴얼에서는 일본의 opening-line 테스트넷을 사용하였습니다. (https://sym-test-01.opening-line.jp:3001/node/info)

- 테스트넷(Testnet) 목록
  - https://symbolnodes.org/nodes_testnet/
- 메인넷(Mainnet) 목록
  - https://symbolnodes.org/nodes/

위 테스트넷 페이지(https://sym-test-01.opening-line.jp:3001/node/info)에 접속 한 뒤, 개발자 도구를 오픈한 뒤 콘솔(console) 화면을 열고 다음 스크립트를 입력합니다.

```js
(script = document.createElement("script")).src =
  "https://xembook.github.io/nem2-browserify/symbol-sdk-pack-2.0.0.js";
document.getElementsByTagName("head")[0].appendChild(script);
```

> **Chrome을 사용하는 경우**  
> 해커의 공격을 대비하기 위해서 "allow pasting"을 콘솔 화면에 입력해서 복사/붙여넣기를 허용해보도록 합시다.

그런 다음 거의 모든 챕터에서 사용되는 공통 로직을 실행합니다.

```js
NODE = window.origin; //The URL of the page is shown here.
sym = require("/node_modules/symbol-sdk");
repo = new sym.RepositoryFactoryHttp(NODE);
txRepo = repo.createTransactionRepository();
(async () => {
  networkType = await repo.getNetworkType().toPromise();
  generationHash = await repo.getGenerationHash().toPromise();
  epochAdjustment = await repo.getEpochAdjustment().toPromise();
})();
function clog(signedTx) {
  console.log(NODE + "/transactionStatus/" + signedTx.hash);
  console.log(NODE + "/transactions/confirmed/" + signedTx.hash);
  console.log("https://symbol.fyi/transactions/" + signedTx.hash);
  console.log("https://testnet.symbol.fyi/transactions/" + signedTx.hash);
}
```

이제 모든 사전 준비가 완료 되었습니다. 🎉  
이 매뉴얼의 내용이 조금 햇갈린다면 [@nem_takanobu](https://qiita.com/nem_takanobu)님이 작성한 Qiita 아티클을 참조하시길 바랍니다. (일본어)

[심볼 블록체인 테스트넷으로 송금을 체험하다.(written by @nem_takanobu)](https://qiita.com/nem_takanobu/items/e2b1f0aafe7a2df0fe1b)
