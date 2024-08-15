# Logic Apps で天気予報 API を作る

## 概要
Here is a simple flow chart:

```mermaid
sequenceDiagram
    participant Alice
    participant Bob
    Alice->>John: Hello John, how are you?
    loop HealthCheck
        John->>John: Fight against hypochondria
    end
    Note right of John: Rational thoughts <br/>prevail!
    John-->>Alice: Great!
    John->>Bob: How about you?
    Bob-->>John: Jolly good!
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