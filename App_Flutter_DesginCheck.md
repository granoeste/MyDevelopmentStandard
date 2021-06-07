# Mobile Flutter App Desgin Check

## Launch / Splash
- ランチスクリーン(スプラッシュ)は必須。 
  - 表示中に Flutter App Engine のロード、App Initialize を行われる。
  - @see https://flutter.dev/docs/development/ui/advanced/splash-screen

## Auth

### Login
- ログイン種類
  - メールアドレス/パスワードの場合
    - メールアドレス入力値正規表現チェック
    - パスワードの表示/非表示切替
  - OAuth
    - Facebook / Google SNS 認証を行う場合は Apple ID 認証必須
- ログイン処理中
  - プログレス表示 や ボタン無効化 など
- ログインエラー時
  - エラー内容のの表示方法

### Sign-In
- サインイン方法

### Invalid Auth
- ログイン中にアクセストークンが無効になった時の挙動

## List View

## Detail View

## Overview
- 各画角(4:3, 19:9, 21,9, 2:1, etc) を意識したデザインか？
- デザインシステム
  - Material Design か？
  - Platform に合わせてコンポーネントを変更するか？
  - Navigation
    - [Buttom Navigation](https://material.io/components/bottom-navigation)
      - Android /iOS で動作異なる。
        - [BottomNavigationBar](https://api.flutter.dev/flutter/material/BottomNavigationBar-class.html)
        - [CupertinoTabScaffold](https://api.flutter.dev/flutter/cupertino/CupertinoTabScaffold-class.html)
    - [Navigation drawer](https://material.io/components/navigation-drawer)
    - [Navigation rail](https://material.io/components/navigation-rail)
  - Theme
    - Button/Switch...など 標準UIコンポーネントを使用するのか？
    - Android /iOS で異なる
    - Button
      - ElevatedButtonTheme
      - OutlinedButtonTheme
      - TextButtonTheme
    - Switch
    - EditText
      - InputDecorationTheme
    - CardTheme
    - SnackBarTheme
    - ChipTheme
    - BottomSheetTheme
    - etc.
- デザイン
  - Android は Buttom/Side にナビゲーションバーが表示されるが考慮しているか？

## Data Loading (In Progress View)
- 処理中のプログレス表示は？
- データ未取得時のUI
- データ取得エラー時のUI
- アイコン/画像
  - 取得中の画像
  - エラー時の画像
  - Placeholder

## Notify
- iOS の通知の許可ダイアログを表示するタイミング
- バックグラウンド時の通知UI
  - iOS / Android で仕様が異なるので それぞれデザインが必要
  - タイトル/ボディ/アイコン
  - 通知タップ時の挙動
- カスタム 通知音 を入れるのか？

## Font
- Web Font(Google Font)を使用するか？

## Text/Edit
- OS設定でフォントサイズを変更していた場合の見た目
- コンポーネントにテキストが収まらないときの挙動
- Edit の Label, Hint, Error のデザイン

## Button
- Elevation or Flat or Outline
- 無効/有効時の見た目
- Elevation
- タップ時の効果

## Card
- Elevation
- タップ時の効果
- 