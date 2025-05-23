---
title: "WordPress エラー apply_filters widget_title"
date: 2019-05-20
category: "プラグイン"
---

おそらく公式ドキュメントの記述が間違っていて、それが理由だと思うのですが、apply\_filters widget\_title でエラー・警告が発生するウィジェットのプラグインが多々あります。

## 1\. エラー・警告の内容

以下の条件を満たすとき、PHP 7.1 より以前では警告 `Warning: Missing argument` が、 PHP 7.1 以降ではエラー `Fatal error: Uncaught ArgumentCountError` が発生します。

発生条件

- ウィジェットの PHP コードに `apply_filters( 'widget_title', $instance['title'] )` (またはもうひとつ引数がある) の記述
- テーマの PHP コードなどで `widget_title` フィルタを追加している

## 2\. 修正方法

ウィジェットの PHP コードの該当箇所を以下のように変更します。

変更前: `apply_filters( 'widget_title', $instance['title'] )`  
変更後: `apply_filters( 'widget_title', $instance['title'], $instance, $this->id_base )`

## 3\. 公式ドキュメントの記述

フィルタのページではフイルタの引数が 3 つ (フィルタ名を含めると 4 つ) の引数が必須となっていますが、「ウィジェット API」のページのサンプルではフィルタの引数が 1 つ (フィルタ名を含めると 2 つ) しかありません。  
多くのウィジェットの開発者はこのページを参照すると思われるので、このミスを含んだウィジェットのプラグインが多々あります。  
  
参考「[Plugin API/Filter Reference/widget title « WordPress Codex](https://codex.wordpress.org/Plugin_API/Filter_Reference/widget_title)」  
参考「[widget\_title | Hook | WordPress Developer Resources](https://developer.wordpress.org/reference/hooks/widget_title/)」  
参考「[WordPress ウィジェット API - WordPress Codex 日本語版](https://wpdocs.osdn.jp/WordPress_%E3%82%A6%E3%82%A3%E3%82%B8%E3%82%A7%E3%83%83%E3%83%88_API)」  
参考「[Widgets API « WordPress Codex](https://codex.wordpress.org/Widgets_API)」

## 4\. エラーが発生するプラグインの具体例

### 4.1. Count Per Day

参考「[Count per Day – WordPress プラグイン | WordPress.org](https://ja.wordpress.org/plugins/count-per-day/)」  
  
バージョン 3.6.1 で確認。

```php
		extract($args, EXTR_SKIP);
		$title = empty($instance['title']) ? ' ' : apply_filters('widget_title', $instance['title']);
		echo $before_widget;
```

```php
	{
		$title = apply_filters( 'widget_title', $instance['title'] );
		$days  = $instance['days' ] + 0;
```
