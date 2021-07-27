# リリース手順
## iOSのリリース
1. AppStoreConnectを開く
2. 新しいバージョンを作成
3. リリースノートを記入
4. ビルドを選択
5. 申請を提出
6. 
### iOSのdsym
1. app/iosに移動
2. bundle exec fastlane upload_dsyms  を実行

## Android
1. PlayConsoleに移動
2. サイドバーの製品版を選択
3. 「新しいリリースを作成」を選択
4. AppBundleの「ライブラリから追加」を選択
5. ビルドを選択
6. リリースノートを記入
7. リリースのレビューをクリック
8. 公開

### Androidのdsym
1. Bitirseのビルドタスクの「APPS & ARTIFACTS」をクリック
2. build.zipをダウンロード（android_dsyms.zipを使う）
3. PlayConsoleに移動
4. サイドバーのAppBundleエクスプローラーをクリック
5. ダウンロードをクリック
6. ネイティブデバッグシンボルにandroid_dsyms.zipを追加
