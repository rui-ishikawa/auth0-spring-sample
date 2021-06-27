# auth0-spring-sample

本ドキュメントでは、IDaaS である Auth0 を使用して認証サーバの作成し、Spring boot のサンプルアプリで Google ログインを行う手順を説明する。

- [auth0-spring-sample](#auth0-spring-sample)
  - [1. Auth0 設定](#1-auth0-設定)
    - [1-1. アカウント作成](#1-1-アカウント作成)
    - [1-2. テナント作成](#1-2-テナント作成)
    - [1-3. アプリケーションの作成](#1-3-アプリケーションの作成)
    - [1-4. アプリケーションの設定変更](#1-4-アプリケーションの設定変更)
  - [2. サンプルアプリ起動](#2-サンプルアプリ起動)
    - [実行環境](#実行環境)
    - [2-1. 接続情報変更](#2-1-接続情報変更)
    - [2-2. 起動](#2-2-起動)
  - [3. Google 認証サーバー設定](#3-google-認証サーバー設定)
    - [3-1. GCP にプロジェクトを作成](#3-1-gcp-にプロジェクトを作成)
    - [3-2. OAuth 同意画面を作成](#3-2-oauth-同意画面を作成)
    - [3-3. 認証情報を設定](#3-3-認証情報を設定)
    - [3-4. Auth0 の設定を更新](#3-4-auth0-の設定を更新)

## 1. Auth0 設定

### 1-1. アカウント作成

https://auth0.com/jp にアクセスし、無料トライアルからアカウント作成。

![sample](./img/ScreenShot%202021-06-27%2016.02.40.png "sample")

### 1-2. テナント作成

Auth0 の Assets(Application、Connection、User)を定義・管理する単位となるテナントを作成する。
任意のテナントドメイン、Region、tag を設定し、「Create」押下。

![sample](./img/ScreenShot%202021-06-27%2016.08.23.png "sample")

テナントが作成されると、ダッシュボードが表示される。
![sample](./img/ScreenShot%202021-06-27%2016.09.44.png "sample")

### 1-3. アプリケーションの作成

認証サーバを利用する Spring アプリケーションを登録する。

画面上部の「Create Application」を押下。
任意のアプリ名を入力、Regular Web application を選択し、「Create」押下。
![sample](./img/ScreenShot%202021-06-27%2016.12.09.png "sample")

Spring boot を選択。
![sample](./img/ScreenShot%202021-06-27%2016.14.02.png "sample")

アプリが作成されると以下のような画面となる。
※gradle プロジェクトであれば「DOWNLOAD SAMPLE」からサンプルアプリを実行可能。
![sample](./img/ScreenShot%202021-06-27%2016.16.22.png "sample")

### 1-4. アプリケーションの設定変更

Settings タブ > 画面中部にある Application URIs で以下を通り設定し、画面下部の Save Changes を押下。

- Allowed Callback URLs: http://localhost:3000/login/oauth2/code/auth0
- Allowed Logout URLs: http://localhost:3000/

![sample](./img/ScreenShot%202021-06-27%2017.08.17.png "sample")

## 2. サンプルアプリ起動

ここでは maven プロジェクトのサンプルアプリを起動する。
リポジトリをクローンし、任意の IDE でプロジェクトを開く。

### 実行環境

- JDK: openjdk version "11.0.11"
- maven: Apache Maven 3.5.4
- IDE: Visual Studio Code

### 2-1. 接続情報変更

/src/main/resources/application.yml を開き、以下の通り値を変更。

- client-id: ダッシュボードの Client ID からコピーした値を設定
- client-secret: ダッシュボードの Client Secret からコピーした値を設定
- issuer-uri: ダッシュボードの Domain からコピーした値を設定

![sample](./img/ScreenShot%202021-06-27%2016.44.33.png "sample")
![sample](./img/ScreenShot%202021-06-27%2016.49.19.png "sample")

### 2-2. 起動

以下を実行し、アプリケーションを起動。

```sh
$ cd ./sample-app/demo
$ mvn spring-boot:run
```

[http://localhost:3000/](http://localhost:3000/) にアクセスすると、以下のようになる。

![sample](./img/ScreenShot%202021-06-27%2016.52.31.png "sample")

「Login」押下すると、Auth0 のログイン画面が表示される。
Auth0 はデフォルトで Google ログインが選択可能。
初回のログインであれば、情報共有の同意画面が表示される。
![sample](./img/ScreenShot%202021-06-27%2016.52.53.png "sample")
![sample](./img/ScreenShot%202021-06-27%2018.20.09.png "sample")

Google 側の認証が成功すると、ログイン後のトップページが表示される。
![sample](./img/ScreenShot%202021-06-27%2016.53.44.png "sample")

ユーザアイコンから Profile を押下し、プロフィール画面に遷移。
画面にアカウント情報と ID トークンが表示される。
![sample](./img/ScreenShot%202021-06-27%2022.21.19.png "sample")

[jwt.ms](https://jwt.ms/) で id token をデコードすると中身を確認できる。
![sample](./img/ScreenShot%202021-06-27%2022.21.28.png "sample")

## 3. Google 認証サーバー設定

Auth0 はデフォルトで google ログインが使用できるが、本番運用には正式な設定を行う必要があります。

### 3-1. GCP にプロジェクトを作成

[Google API Console](https://console.cloud.google.com/)を開く。
画面上部のプロジェクト名を押下 > 新しいプロジェクト を押下。

![sample](./img/ScreenShot%202021-06-27%2017.34.33.png "sample")

任意のプロジェクト名を設定。
![sample](./img/ScreenShot%202021-06-27%2017.41.33.png "sample")

画面上部のプロジェクト名を押下し、作成したプロジェクトに移動。
![sample](./img/ScreenShot%202021-06-27%2017.42.01.png "sample")

作成したプロジェクト名が表示されていることを確認。
![sample](./img/ScreenShot%202021-06-27%2017.45.43.png "sample")

### 3-2. OAuth 同意画面を作成

左のナビゲーション部の OAuth 同意画面を押下。
「外部」を選択し、作成を押下。
![sample](./img/ScreenShot%202021-06-27%2017.48.52.png "sample")

必須項目のアプリ名、ユーザーサポートメール、デベロッパーの連絡先情報を入力。
![sample](./img/ScreenShot%202021-06-27%2017.49.58.png "sample")

承認済みドメインに「auth0.com」を設定。保存して次へを押下。
![sample](./img/ScreenShot%202021-06-27%2018.03.04.png "sample")

スコープとテストユーザーは特に入力不要のため、保存して次へを押下。以下の画面になれば OK。
![sample](./img/ScreenShot%202021-06-27%2017.57.16.png "sample")

アプリを公開を押下し、公開ステータスを本番環境に変更。
![sample](./img/ScreenShot%202021-06-27%2018.30.24.png "sample")

### 3-3. 認証情報を設定

左のナビゲーション部の 認証情報を押下。
認証情報を作成 > OAuth クライアント ID を選択。
![sample](./img/ScreenShot%202021-06-27%2017.59.36.png "sample")

以下のように設定。

- アプリケーションの種類: ウェブアプリケーション
- 承認済み JavaScript 生成元: https://【テナント名】.auth0.com
- 承認済みのリダイレクト URI: https://【テナント名】.auth0.com/login/callback

![sample](./img/ScreenShot%202021-06-27%2018.08.24.png "sample")

OAuth のクライアント ID、クライアントシークレットが表示されるので控えておきます。

![sample](./img/ScreenShot%202021-06-27%2018.08.52.png "sample")

### 3-4. Auth0 の設定を更新

Authentication > Social > google oauth2
Client ID と Client Secret に先ほどコピーした値を入力し、Save changes を押下。

![sample](./img/ScreenShot%202021-06-27%2018.15.33.png "sample")

再度サンプルアプリでログインする。
このとき、Google 側のログイン画面の URL の client_id パラメータの値を確認し、先ほど登録した値と一致していれば正式なクライアントが登録されている。

![sample](./img/ScreenShot%202021-06-27%2018.34.33.png "sample")
