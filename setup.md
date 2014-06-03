このドキュメントでは

## 前提となるソフトウェア
AppPotは複数のJavaアプリケーションサーバー、リレーショナルデータベースをサポートしています。

### Java
+ Java 7

### アプリケーションサーバー

+ Redhat JBoss AS 7.1
+ Oracle WebLogic Server 12c

### データベースサーバー

+ MySQL 5.5
+ Oracle Database 12c
+ SQL Server 2012

OSはアプリケーションサーバーや、データベースサーバー製品がサポートするものがサポートされています。


## セットアップの概要
1. OSのセットアップ
2. 必要なミドルウェアのセットアップ
3. AppPot用のデータベース作成
4. AppPotのデプロイ

1. OSのセットアップ
前提となるソフトウェアで記載したミドルウェアがサポートするOSをセットアップします。
以降の章ではCent OS 6.5を例に記載します。

2. 必要なミドルウェアのセットアップ

3. AppPot用のデータベース作成

AppPot用のデータベースを作成します。
AppPotのデータベースは大きく2つあります。
・AppPot管理用データベース
　AppPotの管理画面で使用されるデータベースです。
　ユーザー、グループ、アプリなどの情報が管理されています。
　AppPotの起動時に自動で生成されます。

・アプリ用データベース
　アプリごとに、アプリのValue Objectに合わせて生成されるデータベースです。
　アプリからAPI（CreateAppDB）が呼び出されたときに生成されます。

AppPotが使用するDBには以下の権限が必要です。
・
・
・

4. AppPotのデプロイ
4.1. AppPotの設定ファイルの変更
4.2. AppPotのデプロイ
4.3. デプロイ後の設定
管理画面を使えるようになるまでの手順です。
例えば、apppot_adminやROLEのセットアップです。
管理画面が使えるようになった後は、別資料があります。