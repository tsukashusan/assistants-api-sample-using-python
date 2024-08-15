# Logic Apps で天気予報 API を作る

## 概要


```mermaid
sequenceDiagram
    autonumber
    actor me
    participant VSCode (Notebook)
    participant Azure OpenAI
    participant Logic Apps
    participant MSN Weather
    
    me ->> +VSCode (Notebook):  今日の東京都港区港南の天気を教えてください?
    VSCode (Notebook)->> +Azure OpenAI: 今日の東京都港区港南の天気を教えてください?
    loop HealthCheck
        VSCode (Notebook)->>VSCode (Notebook): Azure OpenAI 完了確認
    end
    Note left of Azure OpenAI: requires_action 
    Azure OpenAI->> -VSCode (Notebook): 東京都港区港南
    VSCode (Notebook)->>+Logic Apps: 東京都港区港南
    Logic Apps->>+MSN Weather: 東京都港区港南
    MSN Weather->>-Logic Apps: {  "responses": {    "daily": {      "day": {        "cap": "Mostly sunny",   ...
    Logic Apps->>-VSCode (Notebook): {  "responses": {    "daily": {      "day": {        "cap": "Mostly sunny",   ... 
    VSCode (Notebook)->>+Azure OpenAI: {  "responses": {    "daily": {      "day": {        "cap": "Mostly sunny",   ...
    Azure OpenAI->>-VSCode (Notebook): 今日の東京都港区港南の展開は晴れです。 
    VSCode (Notebook)->>-me: 今日の東京都港区港南の展開は晴れです。
```


## STEP 1

[Azure Portal へログイン](http://portal.azure.com)


### 【リソースの作成】を押下

![リソースの作成](./images/create-resource.png)

### 【ロジックアプリ】の【作成】を押下

![ロジック アプリ作成](./images/create-resource-1.png)

### 【従量課金】の【マルチテナント】を選択(◎)し、【選択】を押下

![ロジック アプリのプラン選択](./images/create-resource-2.png)

### 各種作成時のパラメータ設定

1. __リソースグループ__ を作成済みの`rg`から始まる作成済みのリソースグループを選択
1. __ロジック アプリ名__ を`logic-apps-{命名規則}`で作成
1. __地域__ は`Japan East` を選択
1. 【確認および作成】を押下
![alt text](./images/create-resource-3.png)

### 最終確認
【作成】を押下

![最終確認](./images/create-resource-final.png)

### 作成完了

![作成完了](./images/create-resource-complete.png)

### ロジックアプリ 管理画面
ロジックアプリのが表示されるので、【開発ツール】を押下し、【ロジック アプリ デザイナー】を押下。

![Logic Apps ポータル画面](./images/logicapps-portal.png)

### ロジック アプリ デザイナー の表示
ロジック アプリ デザイナーが表示されるので、【トリガーの追加】を押下

![ロジックアプリデザイナー](./images/logicapps-designer.png)

### アプリのデザイン
#### Request (When a HTTP request is received)を追加

1. トリガーの検索ボックスに「http」と入力
1. 検索結果から、【Request (When a HTTP request is received)】を選択

![addHttpRecieved](./images/logicapps-request-add.png)

#### When a HTTP request is recieved の設定

1. 【Method】に「POST」を選択
1. Request Body JSON Schemaに以下のJSONを張り付ける
```json
{
    "type": "object",
    "properties": {
        "location": {
            "type": "string"
        }
    }
}
```