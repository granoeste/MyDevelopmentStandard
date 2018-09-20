# iOS Certificates, Identifiers & Profiles の管理と運用

## Appple Developer (Enterprise) Program 

### iOS Certificates, Identifiers & Profiles の概要

1. Certificates
    1. Development
        1. iOS Development 
            - 開発用ユーザの証明書 ... 開発者(mac)毎にXcodeで作成される。ADPは2つまで？
        2. APNs Development iOS 
            - 開発用APN(Push通知)証明書 ... 有効期限は1年。期限間際に新規作成して追加登録する必要がある。
    3. Production
        1. iOS Distribution 
            - プロダクト/ベータ配布用証明書 ADEPは２つまで。Distribution 用の プロビジョニングプロファイルを作成するために必要
        2. Apple Push Services
            - プロダクト用APN(Push通知)証明書 ... 有効期限は1年。期限間際に新規作成して追加登録し、Push配信アプリに登録が必要となる。
2. Identifiers
    1. iOS App IDs　
        - アプリケーションのBundle ID ... 基本的に 事前に Console から登録する方が良い。Xcodeから登録するとプレフィックスに `XC` が付く。Xcode で Personal Team アカウントを使用して登録すると、Console には表示されないため登録済みであるかさっぱりわからない。
            - Service ... アプリケーションで有効するサービス。Push通知を有効にする場合、APN(Push通知)証明書を設定する必要がある。
3. Devices
    - 開発用に使用しているデバイス
    - ベータ配布用に登録したデバイス
4. Provisioning Profiles
    1. Development
        - 開発用プロビジョニングプロファイル ... Certificatesに開発ユーザーが含まれる必要がある。基本的に Xcode Automatic Provisioning で開発ユーザーが作成する方がよい。ref. [Using match development or Xcode Automatic Provisioning](https://docs.fastlane.tools/codesigning/xcode-project/#using-match-development-or-xcode-automatic-provisioning)
            - iOS Development

    2. Distribution
        - プロダクト/ベータ配布用プロビジョニングプロファイル プロダクト/ベータ配布用証明書を使用して作成する。
            - iOS Distribution 
                - AdHoc : TestFright/Deploygate/Fabricなど ベータ配布用プロビジョニングプロファイル
                - AppStore : Apple AppStore 配布用プロビジョニングプロファイル ... 
            - iOS UniversalDistribution
                - InHouse (Enterprise) : エンタープライズ(社内)配布用プロビジョニングプロファイル
    

## iOS Certificates, Identifiers & Profiles の運用

1. 契約責任者(Agent)が、ADP に契約する。
2. 契約責任者(Agent)は、管理者(Admin)のアカウントを作成、ADP に登録する。
3. 管理者(Admin)は、mac の キーチェーンアクセスから "認証局に証明書を要求(CSR)" (CertificateSigningRequest.certSigningRequest ファイル) を作成する。 
4. 管理者(Admin)は、作成した CSR を指定して配布用証明書(Certificates - Production - iOS Distribution) を作成する。
5. 管理者(Admin)は、ADP コンソールでアプリケーションID(iOS App ID)を登録する。
6. 開発者(Member)は、ADP アカウントを作成し、管理者(Admin)から招待を受ける。
7. 開発者(Member)は、Xcodeから開発証明書(Certificate - iOS Development)を登録する。
8. 管理者(Admin)または開発者(Member)は、開発で使用するiOSデバイスのUUIDを登録する。
9. 開発者(Member)は、 Xcode Automatic Provisioning で、iOS Provisioning Profiles (Development)を登録する。
10. 開発者(Member)は、アプリ開発とテスト
11. 管理者(Admin)は、ADP コンソールで iOS Provisioning Profiles (Distribution)を登録する。
12. 開発者(Member)は、ADP から iOS Provisioning Profiles (Distribution)をダウンロードし、配布用ipa作成、アーカイブを行う。


## fastlane match で管理と運用

iOSでは、アプリを顧客に配布する際にコード署名(Code Signing)とプロビジョニングが必要。
最新のXcodeで自動化させて解決するので簡単にはなっているが、証明書(Certificates)の管理が Mac に依存しており、自動処理によって予期せぬ状態が発生し、チーム開発では複数開発者に影響が発生してしまう。

fastlane match は、開発チームで証明書、プロビジョニングプロファイルを管理するツール。

- [match \- fastlane docs](https://docs.fastlane.tools/actions/match/)
- [Code Signing Guide for Teams](https://codesigning.guide/)
 

### 必要なもの
- fastlane match
- GitHub リポジトリ
- GitHub アカウント 管理者用
- GitHub アカウント 開発者(Member)用 
    - 個々 or 共有アカウント - 共有する場合は fastlane match 専用とする

### 準備
1. fastlane インストール
    ```
    $ brew cask install fastlane
    $ echo 'export PATH=$HOME/.fastlane/bin:$PATH' >> ~/.bash_profile
    $ fastlane --version
    ```
2. GitHub アカウント 管理者用 (ios-developers-admin) の作成
3. GitHub アカウント 開発者共有用 (ios-developers)　の作成
4. SSH 秘密キー/公開キー の作成
    - GitHub へのアクセスは ssh を前提としているため。

### 手順

#### 管理者(Admin)

1. 管理者(Admin)は、コード署名(Code Signing)用の GitHub リポジトリを作成する。
2. GitHub リポジトリのメンバーに、開発者共有用 (ios-developers) を追加する。コミット権限は無しにして読み取り専用にしておく。
3. SSH用の秘密キー/公開キーを作成。

    ```
    $ ssh-keygen -t ed25519 -N "" -f ~/.ssh/github-ios-developers-admin -C ios-developers-admin@foobar.com
    ```

4. キーファイルのパーミッションを修正

    ```
    chmod 600 ~/.ssh/github-ios-developers-admin
    ```

5. GitHub のアカウントに公開キー登録する。
6. .ssh/config に Host を登録
    ```
    Host github-ios-developers-admin
        HostName github.com
        User git
        IdentityFile ~/.ssh/github-ios-developers-admin
        UseKeychain yes
        AddKeysToAgent yes
    ```

7. ssh 疎通確認
    ```
    $ ssh -T github-ios-developers-admin

    The authenticity of host 'github.com (192.30.255.112)' can't be established.
    RSA key fingerprint is SHA256:XXxxx6kXUpJWGl7E1IGOCspXxxTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)? yes   <= yes
    Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
    ```

8. Provisioning Profiles の作成
    - Development

        ```
        fastlane match development \
        --git_url git@github-ios-developers-admin:foobar/ioscertificates.git  \
        --git_user_email foobar@gmail.com \
        --username [Apple Developer Username] \
        --team_id [Team ID] \
        --app_identifier com.example.app
        ```

        - ※作成した Provisioning Profiles の Certificates には、作成者のみしか追加されない。fastlane matchの仕様？ 
        - [Using match development or Xcode Automatic Provisioning](https://docs.fastlane.tools/codesigning/xcode-project/#using-match-development-or-xcode-automatic-provisioning)
        - Development は、Xcode Automatic Provisioning を使用するのが良いのかも...

    - Distribution

        ```
        fastlane match enterprise \
        --git_url git@github-ios-developers-admin:foobar/ioscertificates.git  \
        --git_user_email ios-developers-admin@foobar.com \
        --username [Apple Developer Username] \
        --team_id [Team ID] \
        --app_identifier com.foobar.app
        ```
        
    - **作成時に設定した Passphrase for Git Repo のパスフレーズ は、開発者(Member)に共有する。**


#### 開発者(Member)

1. SSH用の秘密キー/公開キーを作成。

    ```
    $ ssh-keygen -t ed25519 -N "" -f ~/.ssh/github-ios-developers -C ios-developers@foobar.com
    ```

2. キーファイルのパーミッションを修正

    ```
    chmod 600 ~/.ssh/github-ios-developers
    ```

3. GitHub のアカウントに公開キー登録する。
4. .ssh/config に Host を登録
    ```
    Host github-ios-developers
        HostName github.com
        User git
        IdentityFile ~/.ssh/github-ios-developers
        UseKeychain yes
        AddKeysToAgent yes
    ```

5. ssh 疎通確認
    ```
    $ ssh -T github-ios-developers

    The authenticity of host 'github.com (192.30.255.112)' can't be established.
    RSA key fingerprint is SHA256:XXxxx6kXUpJWGl7E1IGOCspXxxTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no)? yes   <= yes
    Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
    ```

6. Provisioning Profiles の取得

    - Distribution

        ```
        fastlane match enterprise \
        --git_url git@github-ios-developers:foobar/ioscertificates.git  \
        --git_user_email ios-developers@foobar.com \
        --team_id [Team ID] \
        --app_identifier com.foobar.app \
        --readonly true <= 読み取り専用
        ```

7.  Provisioning Profiles の一覧

    ```
    $ls -l ~/Library/MobileDevice/Provisioning\ Profiles/
    -rw-r--r--  1 ios-developers  staff  9923  8 31 16:39 4e697e0f-8f0b-43e7-8edc-ef045ebddcb4.mobileprovision
    -rw-r--r--  1 ios-developers  staff  9911  8 31 16:39 7ab41370-f6c9-4262-9aad-9b7b165df96b.mobileprovision
    ```

8.  Provisioning Profiles の確認

    ```
    $ security cms -D -i ~/Library/MobileDevice/Provisioning\ Profiles/4e697e0f-8f0b-43e7-8edc-ef045ebddcb4.mobileprovision
    ```

9.  Xcode の Automatically manage signing をやめる

    - Xcode -> [Target] -> General 
        - -> Signing -> [ ] Automatically manage signing <= チェックを外す
        - -> Signing (Release) -> Provisioning Profile <= ダウンロードした  Enterprise Distribution Provisioning Profiles  [match InHouse com.foobar.app] を選択

##### Environment for fastlane match
match のパラメータを環境変数に設定する場合

```
MATCH_GIT_URL=git@github-ios-developers:foobar/ioscertificates.git
MATCH_GIT_USER_EMAIL=ios-developers@foobar.com
MATCH_KEYCHAIN_PASSWORD=[password]
MATCH_PASSWORD=[password]
MATCH_READONLY=true
```

## デバイス登録

- iOS の UDID 確認方法
    ```
    $ brew install ideviceinstaller
    $ idevice_id -l
    ```
    または、
    ```
    $ system_profiler SPUSBDataType
    $ system_profiler SPUSBDataType | sed -n -e '/iPad/,/Serial/p' -e '/iPhone/,/Serial/p' | grep "Serial Number:" | awk -F ": " '{print $2}'
    ```
    または、
    ```
    $ instruments -s devices
    ```

- fastlane register_devices で追加

    - fastlane/Fastlane　ファイルに lane を作成

        ```
        default_platform(:ios)

        platform :ios do
            desc "Register Device"
            lane :add_device do |options|
                if options[:name] && options[:udid]
                    register_devices(devices: {options[:name] => options[:udid]})
                else
                    UI.error "Usage: fastlane add_device name:'New device name' udid:'UDID'"
                end
            end
        end
        ```

    - 実行

        パラメータに name と UDID を指定して実行

        ```
        $ fastlane add_device name:'Luka iPhone 6' udid:'1234567890123456789012345678901234567890'
        ```

- Provisioning Profiles の再作成
    プロビジョニングプロファイルにデバイスを追加する。

    - Distribution

        ```
        fastlane match enterprise \
        --git_url git@github-ios-developers-admin:foobar/ioscertificates.git  \
        --git_user_email ios-developers-admin@foobar.com \
        --username [Apple Developer Username] \
        --team_id [Team ID] \
        --app_identifier com.foobar.app \
        --force true <= 再作成
        ```

### 参考
- [match \- fastlane docs](https://docs.fastlane.tools/actions/match/)
- [Code Signing Guide for Teams](https://codesigning.guide/)
- [fastlane match でiOSアプリ開発者を「証明書管理の苦しみ」から解放せよ！ \- BizReach Tech Blog](https://tech.bizreach.co.jp/posts/161/ios-manage-certificates/)
- [fastlane matchを使ったiOS証明書、Provisioning Profileの導入管理 \- 百戦錬磨 Techブログ](http://tech.hyakuren.org/entry/ios-provisioning-use-fastlane-match)
- [fastlane match を既存の鍵・証明書・PPで使う – Naohiro Oogatta – Medium](https://medium.com/@oogatta/fastlane-match-%E3%82%92%E6%97%A2%E5%AD%98%E3%81%AE%E9%8D%B5-%E8%A8%BC%E6%98%8E%E6%9B%B8-pp%E3%81%A7%E4%BD%BF%E3%81%86-24471f519f47)
- [hrk's blog: fastlaneでデバイス登録](https://hrk-ys.blogspot.com/2017/07/fastlane.html)
- [リリース自動化だけじゃないfastlane活用方法 \- トレタ開発者ブログ](http://tech.toreta.in/entry/2016/12/01/172454)
- 