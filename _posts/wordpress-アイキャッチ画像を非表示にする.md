---
title: "WordPress アイキャッチ画像を非表示にする"
date: 2019-05-17
category: "小ネタ"
---

**※注意: 一般的にはアイキャッチ画像は非表示にしないべきです。**

特別な理由がある場合にのみ設定してください。

## 1\. コード

function.php に以下の記述を追加。

```php
// アイキャッチ非表示
add_filter( 'has_post_thumbnail', '__return_false' );
```
