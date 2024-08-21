# 作業準備
この章では、ハンズオンの準備を説明しています。以下の手順に従い環境を整えてください。
### 前提条件
- python 3.12 のインストールと` python.exe `が存在しているフォルダパスが環境変数PATHに設定されていること。
- pip が使える環境であること 

### コマンドプロンプトを開く
#### Windows の場合

1. Windowsキー + r を押下
1. cmdと入力し、【OK】を押下</br>
![cmdと入力](./images/command-prompt.png)

#### Mac の場合

- DockでLaunchpadのアイコン をクリックして、検索フィールドに「ターミナル」と入力してから、「ターミナル」をクリックします。
- Finder で、「/アプリケーション/ユーティリティ」フォルダを開いてから、「ターミナル」をダブルクリックします。
### virtualenv が未インストールの方
個別の環境構築のため、Pythonの仮想環境を作成します。
```
pip install virtualenv
```
### virtualenvを使って仮想環境を作成
```
python -m virtualenv -p  python3.12 extension-lectures
```

### アクティベート
```
.\extension-lectures\Scripts\activate.bat
```

### パッケージのインストール
Pythonを実行するにあたり、
```
pip install -r requirements.txt
```

### Visual Studio Code の拡張機能
#### CTRL+SHIFT+X または 拡張機能をクリック

#### Python 拡張のインストール
【インストール】を押下
![Python拡張のインストール](./images/extension-python.png)

#### Jupyter 拡張のインストール
【インストール】を押下
![Jupyter拡張のインストール](./images/extension-jupyter.png)

#### インストール中
![インストール中](images/extension-install.png)

#### インストール完了
インストール後、Visual Studio Codeを再起動する。
【インストール】が消えていればインストール完了。
![インストール完了](images/extension-python-complete.png)

#### REST Client 拡張のインストール
【インストール】を押下
![REST Client 拡張のインストール](./images/extension-REST-API.png)

#### インストール完了
【インストール】が消えていればインストール完了
![インストール完了](./images/extension-REST-API-completed.png)

### Azure OpenAI Studioを使って、gpt-4oのモデルをデプロイ
#### Azure OpenAI Studioへアクセス

![Azure OpenAI Studioへ移動](./images/move-openai-studio.png)

#### 【新しいエクスペリエンスを探索する】を押下

![新しいエクスペリエンスを探索する](./images/new-experience.png)

#### モデルのデプロイ

![＋モデルのデプロイを押下](./images/modeldeploy-1.png)
#### 基本モデルをデプロイする

![基本モデルをデプロイ](./images/modeldeploy-2.png)
#### gpt-4oを選択し、【確認】を押下

![alt text](./images/selection-gpt4-o.png)

#### 【グローバル標準】を選択

![alt text](images/selection-global.png)

####  トークンレート制限を450Kに設定し、【デプロイ】を押下

![alt text](images/token-rate-450K.png)

#### 作成したモデルのモデル名(gpt-4o)をクリップボードにコピーし、.envのAZURE_OPENAI_MODEL_NAMEの値として貼り付ける

![alt text](images/model-name.png)


### ` .env ` を編集
#### Azure Portal から「キー1の値」と「エンドポイントのURL」貼り付ける。また、作成したモデル名(gpt-4-o)を貼りつける

※GET_WEATHER_URLは別の工程で値を追記

![キー1](images/key-endpoint.png)
```python:.env
AZURE_OPENAI_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 「キー1の値」
AZURE_OPENAI_ENDPOINT=https://xxxx-xxxx-xxxx.openai.azure.com/「エンドポイントURL」
AZURE_OPENAI_MODEL_NAME=gpt-4o 「モデル名」
GET_WEATHER_URL=
```

### ハンズオンの開始
準備が完了したので、
[assistants-api.ipynb](../assistants-api.ipynb)へ移動


[README に戻る](../README.markdown)
