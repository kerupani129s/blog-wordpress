---
title: "WordPress プラグイン Search Regex のバグ"
date: 2019-05-18
category: "プラグイン"
---

※2019/05/18 現在、バージョン 1.4.16 での内容です。

使用する上ではあまり問題ないのですが、プラグイン "Search Regex" はバグがあり、検索結果画面で PHP の出力が途中で停止するので、これを何とかします。

## 1\. エラーが発生する箇所

##### search-regex/view/results.php

```php
		</ol>

		<img src="<?php echo plugins_url( '/images/small.gif', $this->base_url() ); ?>" style="display: none" alt="pre"/>

		<script type="text/javascript" charset="utf-8">
```

ここの `$this` はプラグインが定義しているクラス `SearchRegex` で `base_url()` メソッドが定義されておらず、エラー `Uncaught Error: Call to undefined method SearchRegex::base_url()` が発生します。

## 2\. 修正箇所

クラス `SearchRegex` で、`base_url()` メソッドを定義します。

##### search-regex/search-regex.php

```php
	}

	function base_url() {
		return __FILE__;
	}
}
```
