# __221616.com FullRenewal Phase3__
# 統合受付API概要設計書


## 目次
1. 本資料に関して
1. API機能概要
1. 基本仕様
1. 機能一覧
1. 実装方針
1. 補足・注意
1. 画面遷移とAPI呼び出しイメージ
1. API機能別概要
1. 査定申し込み
1. 中古車提案申し込み
1. 来店予約申し込み
1. 見積もり依頼・在庫確認申し込み

---



## 本資料に関して
- 本資料はガリバーWEBシステム(221616.com)が提供する内部APIのうち「統合受付API」について機能概要を記述したものである。
-  本WEBAPI利用者は、かならず本資料の最新版を取得し、内容の十分な精査を行うこと。
 

### API機能概要
- 統合受付API（以下当API）にて提供する機能は下記のとおりとする。
- 提供するAPIのリクエストパラメータ、レスポンスパラメータの詳細は詳細設計にて定めるものとする。


### 基本仕様

当APIの基本構成は下記とする。

|項番|項目|例|備考|
|:--:|:--|:--|:--|
|1|プロトコル|https://|セキュア化を考慮する。|
|2|ドメイン|221616.com||
|3|API|/api/||
|4|バージョン|/v1/|アプリ側の対応時期を考慮しバージョンはURLに含める。指定なき場合はV1とする。|
|5|リクエスト方式|POST|当API提供機能は、新規登録処理ためGET,PUT,DELETEメソッドは使用しない。|
|6|レスポンス値形式|'{"result":0}'|JASON形式にて値を戻す。|


### 機能一覧

|項番|機能名称|API概要|
|:--:|:--|:--|:--|
|1|査定申し込み|査定申し込み内容をDBへ追加する|
|2|中古車提案申し込み|中古車提案内容をDBへ追加する|
|3|来店予約申し込み|来店予約内容をDBへ追加する|
|4|見積もり依頼・在庫確認申し込み|見積もり依頼、在庫確認内容をDBへ追加する|
|5|廃車申し込み|廃車の申し込み内容をDBへ追加する|


### 実装方針
- APIとしては各機能を統合するが、内部的な処理は分離し相互の結合度を疎とするものとする。
- 各入力地のバリデーションはフロント側にて実施するものとする。



### 補足・注意／検討事項

- 上記機能は全て新規情報追加を目的としているが、今後の各申し込み手順として基本情報登録完了後にアンケート情報が追記される事が想定される。その際にメソッドPUTとして実装するか、全く別のAPIを新規に作成するか検討が必要である。
- 初版作成(2016/06/01)時点にて登録先データベースが明確となっていない。
- 各処理の判断に機能毎エンドポイント分割するかリクエストパラメータとして引き渡すか検討する。
- 登録項目の増加が発生した場合のメンテナンス性（DB,カラムを定義ファイルとして保持する等）考慮する。
 

### 画面遷移とAPI呼び出しイメージ

当APIの呼び出し元として下記遷移パタンが想定される

- 入力画面　→　確認画面　(統合APIコール)→　完了画面
- 入力画面　(統合APIコール)→　完了画面
- 入力画面　(統合APIコール)→　アンケート画面　→　確認画面　→　完了画面

---


 
# API 機能別概要

## 査定申し込み
###機能:
査定申込み、廃車申し込み情報をテーブルへ書き込む
###ポスト値:

|項目名|項目内容|型|参考桁数|必須|
|:--|:--:--|:--|--:|:--:|
|MAKER_CD|メーカーコード|半角数字|3桁|○|
|CAR_CD|車種コード|半角数字|6桁|○|
|YEAR|年式|半角数字|4桁|○|
|MILEAGE|走行距離|文字列|20Byte以内|○|
|FNAME|名|文字列|100Byte以内|○|
|LNAME|姓|文字列|100Byte以内|○|
|TEL|電話番号|半角数字|15桁以下|○|
|EMAIL|メールアドレス|文字列|100Byte以内|—|
|USER_COMMENT|自由記入欄|文字列|800Byte以内|－|
|STATE_CD|都道府県コード|半角数字|2桁|○|
|MEDIA_CD|MIKのM|半角数字|4桁|—|
|INSERTION_CD|MIKのI|半角数字|6桁|—|
|KIND_CD|MIKのK|半角数字|9桁|—|
|IP_ADDRESS|申込者のIPアドレス|文字列|15Byte以内|○|
|SITE_CD|書き込みサイトコード※|半角英数字|5桁|○|
|CONTACT_METHOD|連絡手段（１:TEL/2:メール）|半角英数字|1桁|○|
|ENQUETE_IN|アンケートの有無(1:あり / 0:無し)|半角英数|1桁|○|
|FUNCTION|1:査定/5:廃車|半角英数|1桁|○|
|IDOM_ID|サイト横断型ID（COOKIE値）|文字列※|※|○|
|HC_ID|ハートコア出力ID|半角英数字※|※|○|
|SES_ID|セッションID|半角英数字※|※|○|
|NOTES|固定値外の情報。施策用項目等|文字列|※|—|
|CONNECTED_WITH|1:CC/2:クルコネ→連絡手段で判断できない？|半角数値|1桁|？※|


### レスポンス値

|項目名|内容|
|:---|:---|
|Result|0 (正常) / 0以外(異常)|
|message|OK / NG|


###レスポンス例

```
{
  "result": 0,
  "message": "OK"
}
```



## 中古車提案申し込み
### 能概要:
中古車提案申し込み情報をテーブルに書き込む
### ポスト値:

|項目名|項目内容|型|参考桁数|必須|
|:--|:--:--|:--|--:|:--:|
|FNAME|名|文字列|100Byte以内|○|
|LNAME|姓|文字列|100Byte以内|○|
|TEL|電話番号|半角数字|15桁以内|○|
|EMAIL|メールアドレス|文字列 |100Byte以内|－|
|MAKER_CD|メーカーコード|半角数字|3桁|－|
|CAR_CD|車種コード|半角数字|6桁|－|
|TYPE_CD|ボディタイプコード|半角数字|2桁|－|
|MODEL_YEAR|年式|半角数字|4桁|－|
|LOWER_PRICE|予算(低)|半角数字|10桁以内|－|
|UPPER_PRICE|予算(高)|半角数字|10桁以内|－|
|IMPORTANCE1|重視項目1|半角数字|2桁以内|－|
|IMPORTANCE2|重視項目2|半角数字|2桁以内|－|
|IMPORTANCE3|重視項目3|半角数字|2桁以内|－|
|TRADE_IN|下取り有無(1:あり / 0:無し)|半角数字|1桁|－|
|USER_COMMENT|自由記入欄|文字列|800Byte以内|－|
|MEDIA_CD|MIKのM|半角数字|4桁|－|
|INSERTION_CD|MIKのI|半角数字|6桁|－|
|KIND_CD|MIKのK|半角数字|9桁|－|
|IP_ADDRESS|申込者のIPアドレス|文字列|15Byte以内|○|
|SITE_CD|書き込みサイトコード|半角英数字|5桁|○|
|CONTACT_METHOD|連絡手段（１:TEL/2:メール）|半角英数字|1桁|○|
|ENQUETE_IN|アンケートの有無(1:あり / 0:無し)|半角英数|1桁|○|
|FUNCTION|2：中古車提案申し込み|半角英数|1桁|○|
|IDOM_ID|サイト横断型ID（COOKIE値）|文字列※|※|○|
|HC_ID|ハートコア出力ID|半角英数字※|※|○|
|SES_ID|セッションID|半角英数字※|※|○|
|NOTES|固定値外の情報。施策用項目等|文字列|※|—|
|CONNECTED_WITH|1:CC/2:クルコネ→連絡手段で判断できない？|半角数値|1桁|？※|


### レスポンス値

|項目名|内容|
|:---|:---|
|Result|0 (正常) / 0以外(異常)|
|message|OK / NG|


###レスポンス例

```
{
  "result": 0,
  "message": "OK"
}
```



## 来店予約申し込み
### 機能概要:
来店申し込み情報をテーブルに書き込む
### ポスト値:

|項目名|項目内容|型|参考桁数|必須|
|:--|:--:--|:--|--:|:--:|
|FNAME|名|文字列|100Byte以内|○|
|LNAME|姓|文字列|100Byte以内|○|
|TEL|電話番号|半角数字|15桁以内|○|
|EMAIL|メールアドレス|文字列|100Byte以内|－|
|VISIT_KIND|来店種別(0:売りたい / 1:買いたい)|半角数字|1桁|○|
|DEPT_ID|申し込み店舗ID|半角英数字|6桁|○|
|MEDIA_CD|MIKのM|半角数字|4桁|－|
|INSERTION_CD|MIKのI|半角数字|6桁|－|
|KIND_CD|MIKのK|半角数字|9桁|－|
|IP_ADDRESS|申込者のIPアドレス|文字列|15Byte以内|○|
|SITE_CD|書き込みサイトコード|半角英数字|5桁|○|
|CONTACT_METHOD|連絡手段（１:TEL/2:メール）|半角英数字|1桁|○|
|ENQUETE_IN|アンケートの有無(1:あり / 0:無し)|半角英数|1桁|○|
|FUNCTION|3：来店予約申し込み|半角英数|1桁|○|
|IDOM_ID|サイト横断型ID（COOKIE値）|文字列※|※|○|
|HC_ID|ハートコア出力ID|半角英数字※|※|○|
|SES_ID|セッションID|半角英数字※|※|○|
|NOTES|固定値外の情報。施策用項目等|文字列|※|—|
|CONNECTED_WITH|1:CC/2:クルコネ→連絡手段で判断できない？|半角数値|1桁|？※|


### レスポンス値

|項目名|内容|
|:---|:---|
|Result|0 (正常) / 0以外(異常)|
|message|OK / NG|


###レスポンス例

```
{
  "result": 0,
  "message": "OK"
}
```



## 見積もり依頼・在庫確認申し込み
### 機能概要:
見積もり依頼、在庫確認情報をテーブルに書き込む
###ポスト値:

|項目名|項目内容|型|参考桁数|必須|
|:--|:--:--|:--|--:|:--:|
|FNAME|名|文字列|100Byte以内|○|
|LNAME|姓|文字列|100Byte以内|○|
|TEL|電話番号|半角数字|15桁以内|○|
|EMAIL|メールアドレス|文字列|100Byte以内|－|
|EXHIBITION_ID|個車固有ID|半角数字|8桁|○|
|KIND_FLAG|依頼種別(0:見積もり依頼 / 1:在庫確認)|半角数字|1桁|○|
|MEDIA_CD|MIKのM|半角数字|4桁|－|
|INSERTION_CD|MIKのI|半角数字|6桁|－|
|KIND_CD|MIKのK|半角数字|9桁|－|
|IP_ADDRESS|申込者のIPアドレス|文字列|15Byte以内|○|
|SITE_CD|書き込みサイトコード|半角英数字|5桁|○|
|CONTACT_METHOD|連絡手段（１:TEL/2:メール）|半角英数字|1桁|○|
|ENQUETE_IN|アンケートの有無(1:あり / 0:無し)|半角英数|1桁|○|
|FUNCTION|4：在庫確認申し込み|半角英数|1桁|○|
|IDOM_ID|サイト横断型ID（COOKIE値）|文字列※|※|○|
|HC_ID|ハートコア出力ID|半角英数字※|※|○|
|SES_ID|セッションID|半角英数字※|※|○|
|NOTES|固定値外の情報。施策用項目等|文字列|※|—|
|CONNECTED_WITH|1:CC/2:クルコネ→連絡手段で判断できない？|半角数値|1桁|？※|


### レスポンス値

|項目名|内容|
|:---|:---|
|Result|0 (正常) / 0以外(異常)|
|message|OK / NG|


###レスポンス例

```
{
  "result": 0,
  "message": "OK"
}
```
