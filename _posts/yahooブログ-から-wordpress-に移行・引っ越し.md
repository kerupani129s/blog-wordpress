---
title: "Yahoo!ブログ から WordPress に移行・引っ越し"
date: 2019-05-12
category: "引っ越し"
---

**※作業は自己責任でお願いします。**

準備として、サーバー・ドメインや WordPress を使える状態にしておいてください。  
  
参考「[初期設定 - Blog WordPress](../README.md#初期設定)」

ここで説明する方法では以下のデータを移行することができます (それ以外は移行しません) 。

- 公開している記事すべて
- それらの記事のコメント (の一部)
- 画像 (少し小さくなったサイズのみ)

## 1\. FC2ブログ を経由して記事・コメント・画像を保存

Yahoo!ブログ にエクスポートの機能がないので、FC2ブログ を経由して記事・コメント・画像のデータを保存します。

### 1.1. Yahoo!ブログ から FC2ブログ に取り込み

参考「[FC2ブログ > FC2ブログにブログをお引っ越ししよう!! > ブログお引っ越しツール](https://blog.fc2.com/import/register.php)」  
  
上記のページで手続きをしたら、完了通知メールが届くのを待ちます。

### 1.2. FC2ブログ をプライベート (非公開) にする

管理ページから「設定 > 環境設定」のページに行き、メニューから「ブログの設定 > アクセス制限の設定」の設定を表示します。  
設定を以下のように変更します。

- 公開設定: **プライベート**
- ブログ閲覧パスワード: **(パスワード)**

### 1.3. FC2ブログ から記事・コメント・画像を保存

※注意: 登録後 1 週間は FC2ブログ の制限で、保存することができません。

管理ページから「設定 > 環境設定」のページへ移動。

#### 1.3.1. 記事・コメント

「エクスポート > 記事全部 > 全ての記事」の「ダウンロード」から**ダウンロード**します。  
このとき、**記事の件数をメモ**しておきます。  
FC2ブログ が取り込めるのは公開記事のみであり、公開記事のみの件数になっていることに注意してください。

#### 1.3.2. 画像

「ファイルバックアップ > 画像ファイル」のリストを 1 つずつ開き、「名前を付けて保存 > Webページ、完全」で画像ファイルを**一括保存**します。

#### 1.3.3. 文書と誤認識された画像

画像形式によっては文書ファイルと誤認識されることがあり、それらは「ファイルバックアップ > 文書ファイル」から**保存**します。  
リストを開き、そこからリンクを 1 つずつ右クリックして保存する場合などは大丈夫ですが、それぞれのリンクを開いて 「名前を付けて保存」 した場合、画像データが壊れる場合があることに注意してください。  
**拡張子**が正しくないものになっているので、正しく変更します。

## 2\. WordPress でのインポートの準備

### 2.1. インポートツールのインストール

**"Movable Type・TypePad インポートツール"** をインストールします。  
  
参考「[Movable Type・TypePad インポートツール – WordPress プラグイン | WordPress.org](https://ja.wordpress.org/plugins/movabletype-importer/)」

### 2.2. インポートツールのコード修正

2019/05/12 現在、このプラグイン (バージョン 0.4) にはバグがあり、記事がすべてインポートされないことがあります。  
このバグは、インポートするデータの 1 行あたりの最大文字数が設定されているためです。  
以下のようにコードを編集してからインポートツールを使用します。

##### movabletype-importer/movabletype-importer.php

```php
	// function fgets($fp, $len=8192) {
	function fgets($fp, $len=819200) {
		if ( $this->has_gzip() )
			return gzgets($fp, $len);
		return fgets($fp, $len);
	}
```

### 2.3. "Search Regex" インストール

記事を一括で修正するときに使用するプラグインです。  
  
参考「[Search Regex – WordPress プラグイン | WordPress.org](https://ja.wordpress.org/plugins/search-regex/)」

## 3\. WordPress にインポート

### 3.1. 記事・コメント

管理画面から「ツール > インポート」のページに行き、「Movable Type と TypePad」の「インポーターの実行」。  
  
このとき、インポートツールを修正していないと、記事のインポートに失敗しているにもかかわらずすべてインポートできたかのような画面が表示されることに注意してください。  
FC2ブログに取り込んだ記事の件数と、「投稿 > 投稿一覧」のページでのインポートした記事の件数が一致していることを確認してください。

### 3.2. 画像

※注意: 画像をアップロードする前に、サムネイル生成の設定を行ってください。  
  
参考「[3.2.4. メディア設定 - WordPress 初期設定 - Blog WordPress](wordpress-初期設定.md#324-メディア設定)」

管理画面から「メディア > ライブラリ」のページに行き、画像をまとめてアップロードします。

## 4\. インポートデータの修正

### 4.1. 画像 URL の置換

画像 URL が FC2ブログのものになっているので、自サイト用の URL に置換します。  
  
※注意: FC2ブログ の画像 URL はひとによって異なりますので、各自で調べてください。  
※注意: WordPress のメディアの設定で「アップロードしたファイルを年月ベースのフォルダに整理」を ON にしている場合は、画像をアップロードした年月のフォルダを置換後の URL に含めてください。  
※注意: 置換後の URL は、ドメインを含まない絶対パスにするのが良いです。

「ツール > Search Regex」で以下のように指定します。  
  
※注意: 以下は例です。URL をひとそれぞれ内容を書き換える必要があるので、コピー＆ペーストで使用しないでください。

- Source: **Post content**
- Search pattern: **https://blog-imgs-000.fc2.com/u/s/e/username/**
- Replace pattern: **https://example.com/blog/wp-content/uploads/**
- Regex: **OFF**

「Replace」で文字列置換をプレビューし、「Replace & Save」で実際に文字列置換を実行して保存します。

一部の WordPress の画像関係のプラグインが、ドメインを含んだ絶対パスを想定していたので、ドメインを含む絶対パスに置換することにしました。  
ドメインを変更した場合は同様に置換してください。

### 4.2. コメントの SECRET, PASS 削除

「ツール > Search Regex」で以下のように指定します。

- Source: **Comment content**
- Search pattern: **/^SECRET: 0\\nPASS:\\n/**
- Replace pattern:
- Regex: **ON**

「Replace」で文字列置換をプレビューし、「Replace & Save」で実際に文字列置換を実行して保存します。

### 4.3. 記事タイトルの文字化け修正 (FC2ブログのバグ)

FC2ブログに取り込む際のバグで、記事タイトルに含まれる特殊記号が HTML エスケープされたものがさらに HTML エスケープされ、HTML エスケープされた文字列がそのまま表示されてしまいます。  
まずは何の文字がエスケープされているかを調べ、その後エスケープされている文字ごとに置換をします。

#### 4.3.1. エスケープされている文字を調べる

「ツール > Search Regex」で以下のように指定します。

- Source: **Post tiitle**
- Search pattern: **/&amp;\[^;\]+;/**
- Regex: **ON**

※注意: "Search Regex" のバグで、HTML エスケープを用いた検索を行うと、検索後に検索文字列が HTML デコードされてしまうので、検索毎に検索文字列を修正してください。

#### 4.3.2. 具体的な文字を修正する

以下は例です。

- Source: **Post title**
- Search pattern: **&amp;**
- Replace pattern: **&**
- Regex: **OFF**

## 5\. その他

### 5.1. 画像の添付

WordPress には、記事と画像を関係を結びつける「添付」という概念があるのですが、上記の方法でインポートした画像は自動で添付されません。  
添付されていなくても画像の表示は可能なので特に問題はないのですが、一部の画像関係のプラグインは添付画像を前提に動作するため、その手のプラグインを使う際には画像を添付する必要があります。

自動で記事内画像を添付するプラグインを探してみたのですが、見つけられませんでした…。  
手動でメディアページで地道に設定するか、代わりにプラグイン "Thumbnail for Excerpts" を改造してプラグインを自作するというのも方法の 1 つになるかもしれません。

### 5.2. アイキャッチ画像

上記の方法でインポートした場合、アイキャッチ画像が設定されません。  
アイキャッチを自動で設定してくれる系列のプラグインは、有料版でないと使えないことが多かったり、添付画像からしか自動で設定されず、上記の方法でインポートしたサイトには使えなかったりします。

何らかの方法で画像を記事に添付済みである場合には、プラグイン "Easy Add Thumbnail" が便利です。  
  
参考「[Easy Add Thumbnail – Plugin WordPress | WordPress.org](https://es.wordpress.org/plugins/easy-add-thumbnail/)」

アイキャッチ画像を設定する代わりに、プラグイン "Thumbnail for Excerpts" で抜粋に記事の最初の画像を表示するのが簡単な代替案です。

**※注意: この手のプラグインで "Auto Post Thumbnail" というものが有名ですが、こちらは現在、アイキャッチ画像の設定機能が使えなくなっているので、使用できません。**
