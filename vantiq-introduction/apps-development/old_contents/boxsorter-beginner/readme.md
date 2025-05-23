# 荷物仕分けアプリケーション (Beginner)

読み取った送り先コードで荷物を仕分けするアプリケーションの開発を体験します。  

> **補足**  
> 物流センターで利用されている荷物仕分けシステムは下記の様に呼ばれています。
>
> - ボックスソーター (Box sorter)
> - スライドシューソーター (Sliding shoe sorter)
> - サーフィンソーター (Surfing Sorter)

## 荷物仕分け (Box Sorter) アプリケーション の学習概要

荷物仕分けアプリケーションの開発を通じて Vantiq の基本機能を学びます。  

### 学習目的

このワークショップの目的は下記のとおりです。

#### 主目的

1. Vantiq を利用してアプリケーションを作る際の全体的な流れを理解する。
1. 次の **Vantiq リソース** の作成方法を理解する。
   1. **Topic** 
   1. **App**
   1. **Type**
1. 次の **Activity Pattern** の使い方を理解する。
   1. **EventStream**
   1. **Enrich**
   1. **Filter**
   1. **LogStream**
1. 外部からの **REST API エンドポイント** として、 **Topic** が利用できることを理解する。
1. **App** の **EventStream** Activity Pattern に **Topic** を指定することで、外部からのデータを **App** に引き渡せることを理解する。
1. **App Builder** でイベントのデータを確認する方法を理解する。

#### 副次目的

1. Vantiq Access Token の発行方法を理解する。
1. Google Colaboratory の利用方法を理解する。
1. CSV ファイルのインポート方法を理解する。
1. Type の Natural Key について理解する。
1. ログの確認方法を理解する

## 荷物仕分けシステムの全体のイメージ

<img src="./imgs/overview.png" width="800">

1. バーコードリーダーで荷物のバーコードを読み取る。
1. 読み取った結果を Vantiq に送信する。
1. Vantiq はその結果を元に仕分けを行う。
1. Vantiq は仕分け指示を制御システムに送信する。
1. 制御システムは仕分け指示に従ってソーターを制御する。

:globe_with_meridians: [実物のイメージはこちら](https://www.youtube.com/watch?v=1LvaiA3N0E8&t=282s)

ワークショップでは Vantiq の担当部分である No.3〜4 を実装します。
> No.1〜2 は、 Google Colaboratory を利用し、 TOPIC に読み取り結果のサンプル情報を送信することで代用します。  
> Google Colaboratory の詳細は [こちら](/vantiq-google-colab/colab_basic_knowledge/readme.md) で解説しています。

## Vantiq で利用するリソースなどの解説

Vantiq リソースや各用語について解説します。

### Topic

![resource_topic.png](./imgs/resource_topic.png)

Vantiq 内部でデータの受け渡しに利用するエンドポイントになります。  
また、外部からデータを受け渡す際の REST API のエンドポイントとして用いることもできます。

### Type

![resource_type.png](./imgs/resource_type.png)

Vantiq 内部でデータを保存するために利用します。  
内部的には NoSQL の MongoDB を利用しています。  
Activity Pattern や VAIL からデータの読み書きが出来ます。  
外部から REST API を用いて、データの読み書きをすることも出来ます。  

主にマスタデータの保存やデータの一時的な保存に利用されることが多いです。  

> **注意**  
> Type は NoSQL のため、 RDB とは異なり、リレーションシップやトランザクション処理は出来ません。  

### App (App Builder)

![resource_app.png](./imgs/resource_app.png)

App は GUI でアプリケーションの作成ができるツールになります。  
あらかじめ用意されている処理のパターンを組み合わせて開発を行います。  
用意されたパターンで対応できない場合は、プログラミングも可能なため柔軟な実装ができます。

## Vantiq で実装する荷物仕分け (Box Sorter) アプリケーション 概要

<img src="./imgs/vantiq-app.png" width="600">

このアプリケーションを実装していきます。  
詳細は次のステップで説明しますが、 `Google Colaboratory から情報を取得` 、 `仕分け` 、 `仕分け指示を表示` という処理を行います。

## 荷物仕分けアプリケーションで利用する Activity Pattern の紹介

このワークショップでは下記の Activity Pattern を利用します。

### EventStream Activity

![activitypattern_eventstream.png](./imgs/activitypattern_eventstream.png)

App を利用する際に必ずルートタスクとして設定されている Activity Pattern が **EventStream** になります。  
**EventStream** はデータの入り口となります。  
**EventStream** の入力元に **Topic** を指定することで、 Vantiq 内部からのデータを受け取ったり、 外部からの HTTP POST されたデータを受け取ることができます。

### Enrich Activity

![activitypattern_enrich.png](./imgs/activitypattern_enrich.png)

イベントに対して Type に保存されているレコードを追加します。  
イベントが通過するたびに Type へのアクセスが発生するため、パフォーマンスの低下には注意してください。

### Filter Activity

![activitypattern_filter.png](./imgs/activitypattern_filter.png)

**Filter** に設定した条件に合致するイベントのみを通過させます。  
条件に合致しなかったイベントは破棄されるため、注意してください。  
複数の **Filter** を利用することで、 `if / else if / else` の様に分岐させることができます。

### LogStream Activity

![activitypattern_logstream.png](./imgs/activitypattern_logstream.png)

イベントデータをログに出力します。  
今回は仕分け指示が正しく行われているかを確認するために利用します。

## 必要なマテリアル

### 各自で準備する Vantiq 以外の要素

以下のいずれかを事前にご用意ください。

- Google Colab
  - Google アカウント（※Google Colaboratory を利用するために使用します）
  - [BoxSorterDataGenerator (Beginner)](/vantiq-google-colab/docs/jp/box-sorter_data-generator_beginner.ipynb)
  - [BoxSorterDataGenerator (Beginner・複数送信用)](/vantiq-google-colab/docs/jp/box-sorter_data-generator_beginner_multi.ipynb)
- Python
  - Python 実行環境
  - [BoxSorterDataGenerator (Beginner)](/vantiq-google-colab/docs/jp/box-sorter_data-generator_beginner.py)
  - [BoxSorterDataGenerator (Beginner・複数送信用)](/vantiq-google-colab/docs/jp/box-sorter_data-generator_beginner_multi.py)

### 商品マスタデータ

- [sorting_condition.csv](./../data/sorting_condition.csv)

### ドキュメント

- [手順](./instruction.md)
