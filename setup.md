このドキュメントではAppPotをセットアップするための手順を記載しています。

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


## セットアップの手順
### セットアップの概要
AppPotのセットアップは次の手順で行います。

1. OSのセットアップ
2. 必要なミドルウェアのセットアップ
3. AppPot用のデータベース作成
4. AppPotのデプロイ

### 1. OSのセットアップ
前提となるソフトウェアで記載したミドルウェアがサポートするOSをセットアップします。
以降の章ではCent OS 6.5を例に記載します。

### 2. 必要なミドルウェアのセットアップ
各ミドルウェアのマニュアルに従ってセットアップを行って下さい。
参考情報として、JBoss、MySQLの組み合わせでのセットアップの情報は[こちら](./setup_JBoss_MySQL.md)を参照してください。


### 3. AppPot用のデータベース作成

AppPot用のデータベースを作成します。
AppPotのデータベースは大きく2つあります。

+ AppPot管理用データベース
　AppPotの管理画面で使用されるデータベースです。
　ユーザー、グループ、アプリなどの情報が管理されています。
　データベースは任意の名前で作成してください。データベース内のテーブルはAppPotの起動時に自動で生成されます。

+ アプリ用データベース
　アプリごとに、アプリのValue Objectに合わせて生成されるデータベースです。
　アプリからAPI（CreateAppDB）が呼び出されたときに生成されます。

AppPotが使用するDBユーザーはアプリ用データベースの作成などを行いますので、グローバルレベルでALLの権限を付与してください。


### 4. AppPotのデプロイ
#### 4.1. AppPotの設定ファイルの変更

AppPotのWarファイルを解凍し、環境に合わせて以下の設定ファイルを変更します。

| 設定ファイル名 | 設定ファイルの場所 | 概要 |
|:-----------|:------------|:------------|
| jboss-web.xml|{AppPot_WAR}/WEB-INF/jboss-web.xml|AppPotのJBoss上でのコンテキストルートを指定します|
| AppPotログ設定ファイル | {AppPot_WAR}/WEB-INF/classes/log4j.properties | ログ出力先を設定します |
| AppPot設定ファイル | {AppPot_WAR}/WEB-INF/classes/stew.properties | AppPotの各種設定情報を設定します。 |
| 管理画面DB接続設定ファイル | {AppPot_WAR}/WEB-INF/classes/META-INF/persistence.xml | AppPot管理画面が使用するDB接続の設定を行います |

##### jboss-web.xml
AppPotの管理画面や、WebAPIを受け付けるURLの一部になるコンテキストルートを指定します。デフォルトのままでも問題ありません。

設定例

    <?xml version="1.0" encoding="UTF-8"?>
    <jboss-web>    
      <context-root>/apppot124</context-root>    
    </jboss-web>

上記の設定例の場合であれば、管理画面のURLは次のようになります。

`http(s)://<ホスト名>:<JBossのリッスンしているポート番号>/apppot124/`


##### AppPotログ設定ファイル
+ log
	+ ログの出力先ディレクトを指定してください。カレント(.)を指定した場合は、JBossの実行ユーザーのホームディレクトリにログファイルが出力されます
+ log4j.appender.Stew
	+ デフォルトの設定（org.apache.log4j.DailyRollingFileAppender）では日次でログファイルがローテーションされます。Log4Jの設定に従って、他のログ出力方式に変更することができます。

設定例

    # Define the root logger with appender Stew
    log = .
    log4j.rootLogger = INFO, Stew
    -Dlog4j.configuration=log4j.properties
    log4j.debug = true
    # Define the Stew appender
    log4j.appender.Stew=org.apache.log4j.DailyRollingFileAppender
    log4j.appender.Stew.File=${log}/apppot_114.log
    log4j.appender.Stew.DatePattern='.'yyyy-MM-dd
    # Define the layout for Stew appender
    log4j.appender.Stew.layout=org.apache.log4j.PatternLayout
    log4j.appender.Stew.layout.conversionPattern=[%d{dd/MM/yyyy HH:mm:ss,SSS}]: [%p]: %m%n
	


##### AppPot設定ファイル
+ stew.certificate.path
	+ サーバー証明書
+  stew.url
	+ AppPotを使用するアプリのためのDBに接続するURL。アプリ用のDBは自動生成され、DBの名前はAppPotの管理用DBの中で管理されます。 
+ stew.username
	+ AppPotを使用するアプリのためのDBに接続するDBユーザーのアカウント
+ stew.password
	+ AppPotを使用するアプリのためのDBに接続するDBユーザーのパスワード
+ root.password
	+ AppPot内のrootユーザーのパスワード。設定を変更してJBossを再起動することで、パスワードが変更されます

設定例

    # To change this template, choose Tools | Templates
    # and open the template in the editor.    
    # default token age hour
    stew.default.token.age = 300
    stew.numberOfPushNotifyService=1
    #multiLanguage
    languageSupports=en_US,ja
    languageDefault=ja
    #Apple push notification
    #Server certificate path  
    stew.certificate.path =/usr/share/jboss-as-7.1.1.Final/cers/
    stew.certificate.password =123
    #Log level ERROR | MONITOR | NONE
    stew.log.level=ERROR
    #Config for customer's server
    stew.url = jdbc:mysql://localhost:3306
    stew.username = developer00
    stew.password = samplepassword
    # Configure WebLogic Data Source for JDBC Connection of Service APIs
    stew.WebLogic.DataSourceName=JDBC_Data_Source_MSSQL
    #Config for web root
    stew.webroot = stewsprint8
    stew.Database_SQLServer_Mode =false
    #Config for supper admin's password
    root.password = 123456

##### 管理画面DB接続設定ファイル 
AppPotの管理画面が使用するデータベースへの接続情報を記載します。

変更するのは下記です。

+ hibernate.connection.username
	+ データベースへの接続ユーザー名
+ hibernate.connection.password
	+ データベースへの接続ユーザーのパスワード
+ hibernate.connection.url
	+ 接続URL
	「3. AppPot用のデータベース作成」で作成したデータベースを使用します。


設定例

        <property name="hibernate.connection.username" value="developer00"/>
        <property name="hibernate.connection.password" value="samplepassword"/>
        <property name="hibernate.connection.url" value="jdbc:mysql://localhost:3306/apppot124?useUnicode=true&amp;characterEncoding=UTF-8"/>
    
設定変更が完了すれば、再度Warファイルにアーカイブしてください。


#### 4.2. AppPotのデプロイ
AppPotのWarファイルをセットアップしたJBossの下記の場所に配置します。
{JBOSS_HOME}/standalone/deployments


#### 4.3. デプロイ後の設定
#### Roleテーブルへロールの追加

デプロイ後、初期状態のRoleテーブルには初期ユーザーのapppot_adminのロールのみが作成されています。

    mysql> select * from Role;
    +--------+-------------+-------------+
    | roleId | description | roleName    |
    +--------+-------------+-------------+
    |      1 | Super Admin | Super Admin |
    +--------+-------------+-------------+
    1 row in set (0.00 sec)
    

この状態では、一般ユーザーが作成できませんので、次のSQLを実行してRoleテーブルにAdmin、Manager、Userロールを追加します。
roleId 5以降で、モバイルアプリで使用する任意のロールを追加することが可能です。

    mysql> use {作成したAppPot管理用データベース}
    mysql> insert into Role (roleId, description, roleName) values (2, 'Admin', 'Admin'), (3, 'Manager', 'Manager'), (4, 'User', 'User');
    Query OK, 3 rows affected (0.03 sec)
    Records: 3  Duplicates: 0  Warnings: 0
     
    mysql> select * from Role;
    +--------+-------------+-------------+
    | roleId | description | roleName    |
    +--------+-------------+-------------+
    |      1 | Super Admin | Super Admin |
    |      2 | Admin       | Admin       |
    |      3 | Manager     | Manager     |
    |      4 | User        | User        |
    +--------+-------------+-------------+
    4 rows in set (0.00 sec)

これでAppPotの設定作業は完了です。
AppPotの管理画面にアクセスして、アプリやユーザーの登録を行って下さい。

