# Azure OpenAI + Logic Apps で天気を取得する(準備編)

## 概要
Azure OpenAI Service の AIである LLM(large language model) は世界中のさまざまなデータから学習した結果をモデルと定義し、そのモデルを使って、未知なる回答を生成するものです。しかしながら、学習データはある時点までのデータで提供されており、最新の Azure OpenAI Service のモデルであっても、2023 年 11 月までのデータしか学習していません。
したがって、最新のデータや特別なデータを LLM に要約を求める場合は、その電文の中にデータを埋め込み問い合わせする必要があります。
その手法を RAG(Retrieval Augmented Generation) と言われています。

本ハンズオンではそのデータ取得元をMSNの天気情報を使って指定した当日の天気をLLMから回答を得ることを実施します。

## RAG のシーケンス
このシーケンス図にある登場人物は以下の通りです。
* VSCode (Notebook)<p>今利用している開発・実行環境</P>
* Azure OpenAI Service<p>OpenAI 社のLLMをAzureのサービスを通してREST API で提供</p>
* Logic Apps<p>Azureで動く一サービスででローコードでプログラムを開発できるツール。今回はMSN Weatherから取得した結果をREST API で応答する</p>
* MSN Weather<p>MSNの天気サービス。Logic Apps のコネクタを使って接続するサービス。天気を回答する</p>

```mermaid
sequenceDiagram
    autonumber
    actor me
    participant VSCode (Notebook)
    participant Azure OpenAI Service
    participant Logic Apps
    participant MSN Weather
    
    me ->> +VSCode (Notebook):  今日の東京都港区港南の天気を教えてください?
    VSCode (Notebook)->> +Azure OpenAI Service: 今日の東京都港区港南の天気を教えてください?
    loop HealthCheck
        VSCode (Notebook)->>+VSCode (Notebook): Azure OpenAI Service 確認中
    
        Note left of Azure OpenAI Service: requires_action 
        Azure OpenAI Service->> -VSCode (Notebook): 東京都港区港南
        VSCode (Notebook)->>+Logic Apps: 東京都港区港南
        Logic Apps->>+MSN Weather: 東京都港区港南
        MSN Weather->>-Logic Apps: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Logic Apps->>-VSCode (Notebook): {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ... 
        VSCode (Notebook)->>+Azure OpenAI Service: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Azure OpenAI Service->>-VSCode (Notebook): 今日の東京都港区港南の展開は晴れです。 
        VSCode (Notebook)->>-VSCode (Notebook): Azure OpenAI Service 確認完了
    end
    VSCode (Notebook)->>+Azure OpenAI Service: 対象スレッドから応答メッセージの取得要求
    Azure OpenAI Service->>-VSCode (Notebook)　: 対象スレッドから応答メッセージの応答
    VSCode (Notebook)->>-me: 今日の東京都港区港南の展開は晴れです。
```


## ロジック アプリのデプロイ
REST API を公開するためのAzure のサービスである ロジック アプリが使えるように Azure のサブスクリプション上に展開します

```mermaid
sequenceDiagram
    autonumber
    actor me
    participant VSCode (Notebook)
    participant Azure OpenAI Service
    box Skyblue ターゲット
    participant Logic Apps
    end
    participant MSN Weather
    
    me ->> +VSCode (Notebook):  今日の東京都港区港南の天気を教えてください?
    VSCode (Notebook)->> +Azure OpenAI Service: 今日の東京都港区港南の天気を教えてください?
    loop HealthCheck
        VSCode (Notebook)->>+VSCode (Notebook): Azure OpenAI Service 確認中
    
        Note left of Azure OpenAI Service: requires_action 
        Azure OpenAI Service->> -VSCode (Notebook): 東京都港区港南
        VSCode (Notebook)->>+Logic Apps: 東京都港区港南
        Logic Apps->>+MSN Weather: 東京都港区港南
        MSN Weather->>-Logic Apps: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Logic Apps->>-VSCode (Notebook): {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ... 
        VSCode (Notebook)->>+Azure OpenAI Service: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Azure OpenAI Service->>-VSCode (Notebook): 今日の東京都港区港南の展開は晴れです。 
        VSCode (Notebook)->>-VSCode (Notebook): Azure OpenAI Service 確認完了
    end
    VSCode (Notebook)->>+Azure OpenAI Service: 対象スレッドから応答メッセージの取得要求
    Azure OpenAI Service->>-VSCode (Notebook)　: 対象スレッドから応答メッセージの応答
    VSCode (Notebook)->>-me: 今日の東京都港区港南の展開は晴れです。
```


### Azure へログイン
[Azure Portal へログイン](http://portal.azure.com)


### 【リソースの作成】を押下

![リソースの作成](./images/create-resource.png)

### 【Logic Apps】の【作成】を押下

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

## ロジック アプリでREST API を作成
概要で説明した、ロジック アプリのHTTP の Request を受ける機能と MSN Weather のコネクタを使い、天気を返却するREST API を作成します

### ロジック アプリ管理画面
Logic Appsのが表示されるので、【開発ツール】を押下し、【ロジック アプリ デザイナー】を押下。

![ロジック アプリポータル画面](./images/logicapps-portal.png)

### ロジック アプリ デザイナー の表示
ロジック アプリ デザイナーが表示されるので、【トリガーの追加】を押下

![Logic Appsデザイナー](./images/logicapps-designer.png)

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

![HTTPの設定](./images/logicapps-http-received.png)

#### MSN Weatherの追加
```mermaid
sequenceDiagram
    autonumber
    actor me
    participant VSCode (Notebook)
    participant Azure OpenAI Service
    participant Logic Apps
    box Skyblue ターゲット
    participant MSN Weather
    end

    me ->> +VSCode (Notebook):  今日の東京都港区港南の天気を教えてください?
    VSCode (Notebook)->> +Azure OpenAI Service: 今日の東京都港区港南の天気を教えてください?
    loop HealthCheck
        VSCode (Notebook)->>+VSCode (Notebook): Azure OpenAI Service 確認中
    
        Note left of Azure OpenAI Service: requires_action 
        Azure OpenAI Service->> -VSCode (Notebook): 東京都港区港南
        VSCode (Notebook)->>+Logic Apps: 東京都港区港南
        Logic Apps->>+MSN Weather: 東京都港区港南
        MSN Weather->>-Logic Apps: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Logic Apps->>-VSCode (Notebook): {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ... 
        VSCode (Notebook)->>+Azure OpenAI Service: {"responses": {"daily": {"day":      {"cap":"Mostlysunny",...{        "cap": "Mostly sunny",   ...
        Azure OpenAI Service->>-VSCode (Notebook): 今日の東京都港区港南の展開は晴れです。 
        VSCode (Notebook)->>-VSCode (Notebook): Azure OpenAI Service 確認完了
    end
    VSCode (Notebook)->>+Azure OpenAI Service: 対象スレッドから応答メッセージの取得要求
    Azure OpenAI Service->>-VSCode (Notebook)　: 対象スレッドから応答メッセージの応答
    VSCode (Notebook)->>-me: 今日の東京都港区港南の展開は晴れです。
```
1. ⊕を選択し、「アクションの追加」を選択
![MSN Weaterの選択](./images/logicapps-add-action.png)

1. 検索窓に「MSN」と入力し、「今日の予報を取得する」を押下
![MSN Wheather](./images/logicapps-todays-wheather.png)

1. 「Create new」を押下
![MSN Wheather Create new](./images/logicapps-create-wheather.png)

1. ⚡マークを押下
![選択肢を表示](./images/logicapps-select-location1.png)

1. Location を選択
![select Location](./images/logicapps-select-location2.png)

1. メートル法を選択
![select meter](./images/logicapps-select-meter.png)

#### Responseの選択
1. ⊕を選択し、「アクションの追加」を選択
![Responseを選択](./images/logicapps-add-action1.png)

1. 検索窓に「response」と入力し、「Response」を押下
![Responseを配置](./images/logicapps-respose.png)

1. ⚡マークを押下

![選択肢を表示](./images/logicapps-select-body.png)

1. 「本文」を選択

![本文を選択](./images/logicapps-select-body-of-letter.png)

1. 「保存」を押下

![保存](./images/logicapps-store.png)

### アプリのテスト
#### テストの準備
1. URLの取得

![alt text](./images/logicapps-get-url.png)

1. ` logicapps.http `ファイルの <URL> に上書きする

![URLの貼り付け](./images/logicapps-paste-url.png)

1. 貼り付け結果

![URL貼り付け結果](./images/logicapps-result-paste.png)

1. API のテスト結果
赤枠のとおりの「HTTP/1.1 200 OK」と表示されると成功

![APIのテスト結果](./images/logicapps-result-api.png)

### 開発環境へURLを設定
#### 「.env」ファイルにURLを張り付ける
赤枠の「GET_WEATHER_URL=」の右辺にURLを張り付ける
![.env](./images/vscode-paste-url.png)

#### 貼り付け結果
この図のように、URLが貼り付ける

![URLの貼り付け結果](./images/vscode-result-of-paste.png)

これで準備は完了です。以下、URLに進む

[Azure OpenAI + Logic Apps で天気を取得する (実行編)](../assistants-api-with-getweather.ipynb)

[README に戻る](../README.markdown)