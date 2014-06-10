### Object Idの仕組みについて

Object Idは各オブジェクトが持つ、一意のIDです。
Object Idの次の命名ルールでAppPotサーバーにて採番されます。

{Value Object名（=テーブル名）}_{レコード生成日時（注）}

+ 注）
レコード生成日時はUNIX TIMEのフォーマットでミリ秒まで。

+ 例えば「ItemImage_1399017463166」というobject idは次のような意味です。

ItemImageというValue Objectの2014/5/02 16:57:43.166に生成されたデータ
