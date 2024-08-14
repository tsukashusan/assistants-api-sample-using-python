# Logic Apps で天気予報 API を作る

## STEP 1

[Azure Portal へログイン](http://portal.azure.com)


<p>【リソースの作成】を押下</p>

![リソースの作成](./images/create-resource.png)

<p> 【ロジックアプリ】の【作成】を押下</p>

![ロジック アプリ作成](./images/create-resource-1.png)

<p> 【従量課金】の【マルチテナント】を選択(◎)し、【選択】を押下</p>

![ロジック アプリのプラン選択](./images/create-resource-2.png)

<p>各種作成時のパラメータ設定</p>

1. __リソースグループ__ を作成済みの`rg`から始まる作成済みのリソースグループを選択
1. __ロジック アプリ名__ を[`logic-apps-{命名規則}`]で作成
1. __地域__ は`Japan East` を選択
1. 【確認および作成】を押下


![alt text](./images/create-resource-3.png)