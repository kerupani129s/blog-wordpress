---
title: "WordPress テーマの設定"
date: 2019-05-10
category: "初期設定"
---

テーマを直接編集せずに (後述の) 子テーマを作成してください。  
初期設定が終わるまで、サイトの色や追加 CSS の設定を**しないでください**。

## 1\. テーマのインストール・有効化

管理画面の「外観 > テーマ」からテーマの設定をします。  
テーマを使う方法はいくつかあります。

- インストール済みのテーマから選ぶ
- 「新規追加」から検索してインストール
- 他のサイトで配布されているテーマをダウンロードして「新規追加」の「テーマをアップロード」からアップロード
- (FTP などで直接テーマをアップロード)

## 2\. 子テーマを作成する

有効化したテーマを自分で編集する前に、この作業を行ってください。  
すぐにテーマを編集する予定がなくても、おおよそ後に編集する場面が出てくるので、あらかじめ子テーマを作成しておきます。  
  
参考「[子テーマ - WordPress Codex 日本語版](https://wpdocs.osdn.jp/%E5%AD%90%E3%83%86%E3%83%BC%E3%83%9E)」

子テーマを使うと表示速度が低下するという情報がありますが、劇的に遅くことはないと思われるのと、保守性を考えると子テーマを使うべきなので、ここでは子テーマを使うことにします。

### 2.1. フォルダとファイルを作成

親テーマ `parenttheme` があるとして、これから作成する子テーマを `parenttheme-myname` とすると、以下のようなファイルを作成する必要があります。

`parenttheme-myname` フォルダを作成し、その中に `style.css` ファイルと `functions.php` ファイルを作成します。

##### parenttheme-myname/style.css

```css
/*
 * Theme Name:  ParentTheme MyName
 * Author:      MyName
 * Template:    parenttheme
 * Version:     1.0
 * Text Domain: parenttheme-myname
 */

```

##### parenttheme-myname/functions.php

```php
<?php

add_action( 'wp_enqueue_scripts', 'theme_enqueue_styles' );
function theme_enqueue_styles() {
	wp_enqueue_style( 'parent-style', get_template_directory_uri() . '/style.css' );
}

```

(`parenttheme` と `myname` は状況によって書き換えてください。)

テーマを外部向けに公開する場合は `style.css` にもっと項目を追加しないといけないですが、自分のサイトでのみ利用する場合はこの項目だけで大丈夫です。  
(公式のドキュメントにはこの項目だけで良いという記述はないので、WordPress のバージョンが異なると使えないかもしれません。少なくとも WordPress 5.2 ではこの項目だけ設定して動作確認済みです。)

ここでは細かい説明を省きますが、ファイルの一番最後にある PHP の閉じタグは省略したほうが良いので、省略しています。  
(「省略することができる」というより「省略したほうが良い」)

### 2.2. アップロード

いくつか方法がありますが、お好みの方法でアップロードしてください。

- フォルダと中身をまとめて ZIP 形式に圧縮し、「新規追加」の「テーマをアップロード」からアップロード
- (FTP などで直接テーマをアップロード)

### 2.3. 子テーマを有効にする

管理画面で、親テーマと別のテーマとして表示されるので、今アップロードした子テーマを有効化します。  
このとき、すでに親テーマに色や追加 CSS の設定をしていると、**それらの設定がリセットされる**ことに注意してください。

## 3\. 子テーマの設定

### 3.1. function.php

以下は function.php の例です。  
  
(そのままコピー＆ペーストしても正しく動作しないことに注意してください。使用している親テーマによって内容を変更する必要があります。)

##### functions.php

```php
// セットアップ後の処理
add_action( 'after_setup_theme', 'my_after_setup_theme', 11 ); // 親 (優先度 10) より後に起動する
function my_after_setup_theme() {

	// 小さいサイズの画像を生成しない
	remove_image_size('theme-post-medium');
	remove_image_size('theme-post-big');
	remove_image_size('theme-post-single');
	remove_image_size('theme-post-wide');

}

// タイトル変更
add_filter( 'document_title_parts', 'my_document_title_parts' );
function my_document_title_parts( $title ) {
	$title['tagline'] = '';
	return $title;
}

// 記号の自動変換を停止
add_filter( 'run_wptexturize', '__return_false' );
```

### 3.2. style.css

お好みで設定を追加。

編集画面で

> ご存知ですか ?  
> ここで CSS を変更する必要はありません — 付随の CSS エディター で、編集とライブプレビューを行うことができます。

というメッセージが表示されますが、子テーマを使うことを前提とする場合、必ずしも「CSS エディター」が良いとは言い切れません。  
ここでは 「CSS エディター」ではなく style.css に設定することをおすすめします。

### 3.3. カスタマイズ・メニュー・ウィジェット

お好みで設定を変更します。  
  
ただし、サイトの色や追加 CSS の設定は子テーマの style.css に記述することをおすすめします。
