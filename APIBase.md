# API標準

## 1. 通信方式

- クライアントとAPIサーバーは、HTTP/1.1で通信する。
- HTTPS必須。但し開発環境の場合に限りHTTPを許可。

- 1つのリクエストには、1つのレスポンスを返却するプル型とする。
- CometやServer-Sent Events のようにサーバー側からデータをプッシュしない。
    - ※プッシュ型のAPIは別途設計を行う。

- リクエストとレスポンスの方式はRESTfulぽい方式とする。
    - RESTのベストプラクティス | 開発手法・プロジェクト管理 | POSTD http://postd.cc/some-rest-best-practice/


## 2. HTTPリクエスト

### 2-1. HTTPヘッダー
HTTPヘッダーには共通項目として以下の項目をからず付与する。

| ヘッダーキー    | 必須 | 値/説明 |
| ------------- |:---:| ---------------- |
| Host          |  O  | 接続先ホスト名 |
| User-agent    |  O  | アプリカスタム。**2-3. User-Agent参照**  ※iOS NSURLConnection のデフォルトは URLCache/1.0 CFNetwork/672.0.8 Darwin/14.0.0 |
| Content-Type  |  O  | application/json |
| Accept-Charset|  O  | utf-8 |
| Cookie        |  O  | APIサーバーから発行されたCookieの内容をそのまま全て設定する。アクセストークンもCookieに含まれる。|


例)

```
Host: exapmle.com
User-Agent: URLCache/1.0 CFNetwork/672.0.8 Darwin/14.0.0
Content-Type: application/json
Accept-Charset: utf-8
Cookie: unique-id=6ebdba5184759678500fb8d9989e1c3d83a3619b; ARRAffinity=31969fd53666e2f721fec9cb1773cd8f990e82fd8dd311ef09858ee8f05170be; access_token=1c32efdf7fe54447aefbe0598abef71b;
```

**API毎に個別の値が存在する場合はそれぞれに記述する。**


#### 2-1-1. CORS 設定
Cross-Origin Resource Sharing (CORS)
WebフロントエンドがAPIサーバーに対してアクセスするため、自身が送信されたドメインとは異なるドメインのリソースに対してクロスサイトHTTPリクエストを行う必要があります。
通常、JavaScriptによって実行されるクロスサイトHTTPリクエストは、セキュリティ上の理由から制限が掛けられています。これを「同一生成元ポリシー（same-origin policy）」といいます。Ajax／XMLHttpRequestより発行されたHTTPリクエストはこの同一生成元ポリシーの制約を受け、Webアプリは自身が送信されたドメインにのみHTTPリクエストを発行でき、他のドメインには発行できません。
CORSは、Webサーバーが異なるドメインからのアクセスを制御することで、異なるドメイン間での通信を安全にするための仕様です。CORSで制御できるルールには以下のようなものがあります。

- クロスドメインアクセスを許可するサーバーのドメイン
- 使用を許可するHTTPメソッド
- 使用を許可するHTTPヘッダー

APIサーバーが、WebフロントエンドのJavaScriptからのアクセスの場合、以下の設定を追加する。

| キー                              | 値                    | 説明                 |
| -------------------------------- | --------------------- | -------------------- |
| Access-Control-Allow-Origin      | example.com           | 許可するOriginドメイン |
| Access-Control-Allow-Methods     | GET、POST、PUT、DELETE | APIで発生するメソッド  |
| Access-Control-Allow-Credentials | true                  | Cookieを含めるので、クレデンシャルを含むリクエストへ応答できるようにします。|


#### 2-1-2. Cookie

*CookieをAPIとWebViewで共通で同じ値を使用することで、セッション情報の連携を行う。*


Cookieのセキュア設定
- secure - HTTPS以外では参照できなくする。指定しない。
- HttpOnly - JavaScriptから参照できなくする。Webフロントエンドで参照するので付与不可。
- Domain - サブドメインには送信するようにする。設定しない。


### 2-2. リクエスト

許可するメソッド

- GET
- POST
- PUT
- DELETE
- HEAD - *(未使用)*
- OPTIONS - *(未使用)* Webフロントエンドで必要になるかも。

各APIのパラメータは、GETの場合はクエリ文字列で、POST/PUTの場合はBODY部に指定する。
DELETEメソッドにBODYを含めない。


GET

```
GET /user?uid=xxxx
```

POST

```
POST /user

{
    "uid":"foobar",
    "pwd":"hogehoge"
}
```

### 2-3. User-Agent

各環境毎のUser-Agentの設定

- iOS HttpClient

    カスタムUser-Agent

    ```
    App/{Name},{BundleID},{Version},{Build},{Model},{OS},{SystemVersion},HttpClient
    ```

    **アプリ付加情報**

    | 項目           | 値               | 取得元            |
    | ------------- | ---------------- | -------------------- |
    | App/          | 固定文字列        | |
    | Name          | MyApp            | NSBundle.mainBundle().objectForInfoDictionaryKey("CFBundleName") as! String |
    | BundleID      | co.example.myapp | NSBundle.mainBundle().bundleIdentifier |
    | Version       | 1.0.0            | NSBundle.mainBundle().objectForInfoDictionaryKey("CFBundleShortVersionString") as! String |
    | Build         | 100              | NSBundle.mainBundle().objectForInfoDictionaryKey("CFBundleVersion") as! String |
    | Model         | iPad             | UIDevice.currentDevice().model |
    | OS         | iOS             | "iOS" |
    | SystemVersion | 9.2              | UIDevice.currentDevice().systemVersion |
    | Client        | HttpClint or WebView | HttpClint:APIを呼び出すとき | 

    例) 
    
    ```
    App/MyApp,com.example.myapp,1.0.0,100,iPad,iOS,9.2,HttpClient
    ```
    
- iOS WebView

    UIWebViewのデフォルトのUser-AgentにカスタムUser-Agentを付加する。

    ```
    {DefaultUserAgent} App/{Name},{BundleID},{Version},{Build},{Model},{OS},{SystemVersion},{Model},{SystemVersion},WebView
    ```

    例) 
    
    ```
    Mozilla/5.0 (iPhone; CPU iPhone OS 9_3_2 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Mobile/13F69 App/MyApp,com.example.myapp,1.0.0,100,iPad,iOS,9.2,WebView
    ```

- Android HttpClient

    カスタムUser-Agent

    ```
    App/{Name},{ApplicationId},{Version},{Build},{Model},{OS},{SystemVersion},HttpClient
    ```

    **アプリ付加情報**

    | 項目           | 値               | 取得元            |
    | ------------- | ---------------- | -------------------- |
    | App/          | 固定文字列        | |
    | Name          | MyApp            |context.getString(context.getApplicationInfo().labelRes) |
    | ApplicationId      | co.example.myapp | BuildConfig.APPLICATION_ID |
    | Version       | 1.0.0            | BuildConfig.VERSION_NAME |
    | Build         | 100              | BuildConfig.VERSION_CODE |
    | Model         | Nexus6             | Build.MODEL |
    | OS         | Android             | "Android" |
    | SystemVersion | 7.0              | Build.VERSION.RELEASE |
    | Client        | HttpClint or WebView | HttpClint:APIを呼び出すとき | 

    例) 
    
    ```
    App/MyApp,com.example.myapp,1.0.0,100,Nexus6,Android,7.0,HttpClient
    ```
- Android WebView

    WebViewのデフォルトのUser-AgentにカスタムUser-Agentを付加する。

    ```
    {DefaultUserAgent} App/{Name},{BundleID},{Version},{Build},{Model},{OS},{SystemVersion},{Model},{SystemVersion},WebView
    ```

    例) 
    
    ```
    Mozilla/5.0 (Linux; Android 7.0; Nexus 6 Build/KRT16M) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/31.0.1650.59 Mobile Safari/537.36 App/MyApp,com.example.myapp,1.0.0,100,Nexus6,Android,7.0,WebView
    ```


- Webフロントエンド

    ブラウザの標準の文字列

    - Safari(iOS) (例)

        ```
        Mozilla/5.0 (iPad; CPU OS 9_3_2 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13F69 Safari/601.1
        ```

    - Chrome(Android) (例)
        ```
        Mozilla/5.0 (Linux; Android 4.0.3; SC-02C Build/IML74K) AppleWebKit/537.31 (KHTML, like Gecko) Chrome/26.0.1410.58 Mobile Safari/537.31
        ```

- Webフロントエンド(PC)

    各ブラウザの標準の文字列

    - Chrome (例)

        ```
        Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.106 Safari/537.36
        ```

    - Safari (例)

        ```
        Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/601.6.17 (KHTML, like Gecko) Version/9.1.1 Safari/601.6.17
        ```

### 2-4. APIのバージョン

URLのベースパスにバージョンを指定する

```
/api/v1/products
```

### 2-5. リソースパス

- リソース名の名詞を使用する
    - NG: [GET] /getUsers
    - **OK**: [GET] /users
- リソースへアクセスはパスをを使用する
    - NG: [GET] /getUser?userId=3
    - **OK**: [GET] /user/3
- スネークケースを使用する
    - NG: [GET] /userTimeline
    - **OK**: [GET] /user_timeline
- 入れ子になったリソースを使用する
    - NG: [POST] /user/photo?userId=3
    - **OK**: [POST] /user/3/photo

※リソースの拡張子(例:/users.json)など、プラットフォーム/フレームワークの固有のルールには柔軟に一貫性を持って対応する

### 2-6. パラメータ

- スネークケースを使用する
    - NG: [GET] /userTimeline?createdAt=2016-07-11T20:36:56+09:00
    - **OK**: [GET] /user_timeline?created?at=2016-07-11T20:36:56+09:00
- ページングはパラメータを使用する
    - NG: [GET] /user_timeline/page/1
    - **OK**: [GET] /user_timeline?page=1

#### *APIパス参考*

| メソッド  | パス                          | 説明                |
| -------- | ---------------------------- | ------------------- |
| [POST]   | /user                        | ユーザーを追加        |
| [PUT]    | /user                        | ユーザーを更新        |
| [GET]    | /user/{userId}               | ユーザーを取得        |
| [DELETE] | /user/{userId}               | ユーザーを削除        |
| [POST]   | /user/{userId}/photo         | ユーザーのフォトを追加 |
| [GET]    | /user/{userId}/friends       | ユーザーの友人を取得   |
| [GET]    | /user/search?birthday={date} | ユーザーを誕生日で検索 |


## 3. HTTP レスポンス

| ヘッダーキー    | 必須 | 値/説明 |
| ------------- |:---:| ---------------- |
| Status        |  O  | ステータス |
| Set-Cookie    |  O  | セッション管理を行うアクセストークン等含まれたCookie |

### ステータス

APIでは、以下のステータスを使用する。

| ステータス | 説明 | 補足 |
| ------ | --- | ------------- |
| 200    | OK  | リクエストは成功。リクエストに応じた内容をセット。 |
| 204    | No Content | リクエストは正常に処理されたのにコンテンツが返されなかった。（いい例が何かを削除する場合です）。|
| 400    | Bad Request | リクエストが不正。API内部でエラーが発生した。 |
| 401    | Unauthorized | 認証が必要である。ログイン/パスワードの認証エラーやアクセストークンの無効になった。 |
| 403    | Forbidden | アクセスが禁止された。 |
| 404    | Not Found | 未検出。対象のデータが存在しない。|
| 500    | Internal Server Error | サーバ内部エラー。サーバ内部にエラーが発生した。|

これら以外の ステータスを使用する場合には、
[HTTPステータスコード](https://ja.wikipedia.org/wiki/HTTP%E3%82%B9%E3%83%86%E3%83%BC%E3%82%BF%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%89) を参考にして設計を行う。

## 4. データ型とフォーマット

使用するデータ型

- **文字列型**
- **数値型**
- **Bool型**
- **浮動小数点型**
- **Date型**

    フォーマットは **ISO 8601 形式 とする！**
    
    ```
    yyyy-MM-dd'T'HH:mm:ssZ
    ```

    | 変換時間             | ISO 8601 形式 |
    | ------------------- | ------------ | 
    | 2016-07-11 20:36:56 | 2016-07-11T20:36:56+09:00 |

    参考)
    
    | フォーマット   | 値 |
    | ------------- | ------------- |
    | Unixtime     | 1468237016 |
    | 日付表記      | 2016/07/11 20:36:56 |
    | ハイフン区切り | 2016-07-11 20:36:56 |
    | 区切りなし     | 20160711 203656 |
    | 日本語表記     | 2016年07月11日 月曜日 20時36分56秒 |
    | **ISO 8601 形式** | 2016-07-11T20:36:56+09:00 | 
    | RFC 2822 形式 | Mon, 11 Jul 2016 20:36:56 +0900 |

    * Unixtime相互変換ツール - konisimple tools http://tool.konisimple.net/date/unixtime
    * ISO 8601 - Wikipedia https://ja.wikipedia.org/wiki/ISO_8601


- サンプル
    ```
    [{
        "id":"qwerty",
        "type": 1,
        "name": "qwerty",
        "url": "http://example.com/movie/qwerty.mp4",
        "validity_start_date": "2016-07-11T20:36:56+09:00" ,
        "validity_end_date": "2016-07-11T20:36:56+09:00",
        "enabled": true,
        "playing_sec": 180,
        "rate": 3.4,
        "thumbnail_url": "http://example.com/image/qwerty.jpg",
        "tags" : ["kids", "summer", "beacth"]
    },{
        "id":"asdfgh",
        "type": 1,
        "name": "asdfgh",
        "url": "http://example.com/movie/asdfgh.mp4",
        "validity_start_date": "2016-07-11T20:36:56+09:00" ,
        "validity_end_date": "2016-07-11T20:36:56+09:00",
        "enabled": true,
        "playing_sec": 180,
        "rate": 4.1,
        "thumbnail_url": "http://example.com/media/asdfgh.jpg",
        "tags" : ["summer", "old_age"]
    }]
    ```

## 5. エラーデータフォーマット

API内部でエラーが発生した場合に、Status 4xx を設定し、Bodyにエラーコードとメッセージを設定してレスポンスを返す。

| プロパティ | データ型 | 説明 |
| ------------------- | ------------ | -- |
|code | 文字列 | エラーコード |
|message | 文字列 | エラーメッセージ |

サンプル
- 401 Unauthorized 

    ```
    {
        "code": "xxxxx",
        "message": "アクセストークが無効になりました。"
    }
    ```
- 500 Internal Server Error

    *Body無し*
    
    内部で致命的なエラーが発生した場合、Status 500 でレスポンスを返す。
    データベースアクセス、他システムからの応答なし等々、システム調査が必要なエラー。
