
# トラブルシューティング

AppPotを運用する上で問題が発生した場合を書きます。

## AppPotのログ

アクセスログ
AppPotのログ

### メモリ不足

AppPotはJavaで動いていますので使用できるメモリ量はJavaの仮想マシンが使用するメモリのサイズに依存します。
大量のデータを一度に取得したり、大量のトランザクションが発生した場合に、メモリが不足することでOutOfMemoryエラーや、処理の遅延が発生する可能性があります。

サービスのリリース前にはサービス稼働後に想定されるデータ量を事前に登録しておき、本番想定のトランザクション量をかけて負荷試験を行うことを推奨します。

その際、次の情報を取得して、メモリが不足していないかチェックしてください。

jvmstatを使用してテスト中のJVMのメモリ使用量の推移を確認。

`jvmstat`


アプリケーションサーバーのログにOutOfMemoryのエラーがでていないか確認。

`{JBoss Home}/standalone/log/server.log`




## チューニング


### データベースチューニング
AppPotのデータベースには自動的にオブジェクトIDにインデックスが生成されています。アプリのデータについては、オブジェクトID以外の項目にインデックスを追加することが許されています。

アプリのデータベースは次の命名ルールで作成されています。
`apppot_{App ID}_{Version}`

例
`apppottrial_jp_co_ncdc_MedichineApp_1_2`

.は_に置換されます。


データベースの例

    mysql> show databases;
    +------------------------------------------+
    | Database                                 |
    +------------------------------------------+
    | information_schema                       |
    | apppottrial_jp_co_ncdc_MedichineApp_1_2  |
    | developer00                              |
    | mysql                                    |
    | stewsprint5                              |
    | stewsprint5_jp_co_ncdc_MedichineApp1_1_0 |
    | stewsprint5_ootani_1_0                   |
    +------------------------------------------+
    14 rows in set (0.00 sec)



アプリデータベースの中はAPObjectを継承して作成したオブジェクトの単位でテーブルが作成されています。TODOこれの事を何と呼ぶの？

    mysql> use stewsprint5_jp_co_ncdc_MedichineApp1_1_0
    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A
    
    Database changed
    mysql> show tables;
    +----------------------------------------------------+
    | Tables_in_stewsprint5_jp_co_ncdc_MedichineApp1_1_0 |
    +----------------------------------------------------+
    | CaseReport                                         |
    | Follow                                             |
    +----------------------------------------------------+
    2 rows in set (0.00 sec)


自動生成されたテーブルの構造は以下のようになっています。インデックスを付ける以外の操作はサポートされていませんのでご注意下さい。

    mysql> desc CaseReport;
    +-------------------------+--------------+------+-----+---------+-------+
    | Field                   | Type         | Null | Key | Default | Extra |
    +-------------------------+--------------+------+-----+---------+-------+
    | isUsingLockForUpdate    | double       | YES  |     | NULL    |       |
    | scopeType               | double       | YES  |     | NULL    |       |
    | createTime              | double       | YES  |     | NULL    |       |
    | updateTime              | double       | YES  |     | NULL    |       |
    | memo                    | text         | YES  |     | NULL    |       |
    | isFollow                | double       | YES  |     | NULL    |       |
    | patientName             | text         | YES  |     | NULL    |       |
    | persistentType          | double       | YES  |     | NULL    |       |
    | scopeTypeForAutoRefresh | double       | YES  |     | NULL    |       |
    | boolTest                | double       | YES  |     | NULL    |       |
    | lifeSpan                | double       | YES  |     | NULL    |       |
    | objectId                | varchar(255) | NO   | PRI | NULL    |       |
    | syncStatus              | text         | YES  |     | NULL    |       |
    | gender                  | double       | YES  |     | NULL    |       |
    | isAutoRefresh           | double       | YES  |     | NULL    |       |
    | age                     | double       | YES  |     | NULL    |       |
    | autoRefreshInterval     | double       | YES  |     | NULL    |       |
    | createUserId            | double       | YES  |     | NULL    |       |
    | groupIds                | text         | YES  |     | NULL    |       |
    | serverCreateTime        | varchar(255) | YES  |     | NULL    |       |
    | serverUpdateTime        | varchar(255) | YES  |     | NULL    |       |
    +-------------------------+--------------+------+-----+---------+-------+
    21 rows in set (0.01 sec)

例1：createUserIdにインデックスを付ける場合

    mysql> ALTER TABLE CaseReport ADD INDEX (createUserId)

例2：groupIds, createUserIdにインデックスを付ける場合

    mysql> ALTER TABLE CaseReport ADD INDEX (groupIds, createUserId)


## マスターデータのロード

アプリの中でマスターデータを登録

# 既存のシステムとの連携

## システム間連携がなぜ必要なのか。






