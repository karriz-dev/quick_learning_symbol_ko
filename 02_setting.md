# 2.개발 환경 구축하기

해당 섹션은 모든 문서를 어떻게 읽어야하는지에 대해 설명합니다.

## 2.1 언어

모든 코드는 자바스크립트(JavaScript)로 작성되었습니다.

### 표준 개발 키트 (Standard Development Kit, SDK)

symbol-sdk-typescript-javascript v2.0.0  
https://github.com/symbol/symbol-sdk-typescript-javascript

브라우저 개발자 콘솔에서 browserify를 통해 SDK를 로드합니다.
https://github.com/xembook/nem2-browserify

##### 노트

현재 symbol-sdk v3.0.0은 알파 버전으로 릴리스되었으며 v2.0.3은 더 이상 사용되지 않습니다.
그리고 버전 3에서는 rxjs 의존적인 기능이 많이 제거되었으므로 REST API에 직접 액세스하는 것이 좋습니다.

### 참조

TypeScript and JavaScript를 위한 심볼 SDK
https://symbol.github.io/symbol-sdk-typescript-javascript/1.0.3/

캐터펄트(심볼 블록체인 Core 프로그램, Catapult) REST 엔드포인트 (1.0.3)  
https://symbol.github.io/symbol-openapi/v1.0.3/

## 2.2 샘플 소스 코드

### 변수 선언

이 문서에서는 콘솔에서 반복적으로 다시 작성하여 작동하는지 확인하기를 원하기 때문에 const를 선언하지 않습니다. 응용프로그램을 개발할 때에는 const를 선언하여 보안을 보장해야 합니다.

### 출력 값 확인

Console.log()는 변수 내용을 출력합니다. 원하는 대로 출력 기능을 사용해보세요. 출력은 `>` 다음에 표시됩니다. 샘플로 연습할 때는 이 부분을 제거하고 사용하세요.

### 동기와 비동기

다른 언어에 익숙한 개발자들은 비동기 처리를 쓰는 것이 불편할 수 있으므로, 특별한 이유가 없는 한 비동기 처리 없이 설명을 작성합니다.

### 계좌 (Account)

#### 엘리스

해당 매뉴얼에서는 엘리스라는 계정에 초점을 맞춥니다. 다음 보게 될 3장에서 부터 앨리스 계정을 계속 사용할 예정입니다. XYM을 충분히 전송한 상태에서 본 설명서를 계속 읽으십시오.

#### 밥

밥은 해당 챕터에서 요구하는 대로 엘리스와의 거래를 위한 계정으로 생성됩니다. 캐롤과 같은 다른 계좌는 이후 다중서명 챕터에서 사용됩니다.

### 수수료

이 문서에서 트랜잭션의 거래 수수료는 승수 100으로 생성됩니다.

## 2.3 Preparations in advance

노드 목록에서 새로운 페이지를 오픈합니다(예: 크롬 브라우저). 해당 매뉴얼에서는 테스트넷을 가정했습니다.

- 테스트넷(Testnet)
  - https://symbolnodes.org/nodes_testnet/
- 메인넷(Mainnet)
  - https://symbolnodes.org/nodes/

F12를 누른 뒤 개발자 도구를 오픈한 뒤 콘솔(console) 화면을 열고 다음 스크립트를 입력합니다.

```js
(script = document.createElement("script")).src =
  "https://xembook.github.io/nem2-browserify/symbol-sdk-pack-2.0.0.js";
document.getElementsByTagName("head")[0].appendChild(script);
```

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

이제 모든 준비가 되었습니다.
이 매뉴얼의 내용이 조금 햇갈린다면 @nem_takanobu님이 작성한 Qiita 아티클을 참조하시길 바랍니다.

[심볼 블록체인 테스트넷으로 송금을 체험하다.(written by @nem_takanobu)](https://qiita.com/nem_takanobu/items/e2b1f0aafe7a2df0fe1b)
