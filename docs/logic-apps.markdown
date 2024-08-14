# Logic Apps で天気予報 API を作る

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