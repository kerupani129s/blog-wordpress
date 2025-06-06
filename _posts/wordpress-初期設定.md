---
title: "WordPress 初期設定"
date: 2019-05-07
category: "初期設定"
---

準備として、サーバーやドメインを使える状態にしておいてください。

(以下の内容は個人的に良いと思った設定内容なので、臨機応変に対応をお願いします。)

## 1\. インストール

### 1.1. 方法1: 手動でインストール

公式サイトから本体をダウンロードし、解凍して、サーバーに FTP などでアップロードします。  
  
参考「[日本語 — WordPress](https://ja.wordpress.org/)」  
  
アップロードしたディレクトリの URL にアクセスし、画面の指示に従ってインストールします。  
データベース情報やユーザー情報などの入力が求められます。  
(「サイトのタイトル」は後から変更できます。)  
**「プライバシー 検索エンジンでの表示 検索エンジンがサイトをインデックスしないようにする」にチェックを入れます。**

### 1.2. 方法2: レンタルサーバーの機能などでインストール

WordPress などの有名な CMS を簡単にインストールできる機能がレンタルサーバーに付いてくることがあります。  
インストール時に以下の設定がない場合は、インストール後に管理画面の「設定 > 表示設定」から同様の設定をします。  
**「プライバシー 検索エンジンでの表示 検索エンジンがサイトをインデックスしないようにする」**

## 2\. 最初に入れるプラグイン

**"SiteGuard WP Plugin"** をインストールします。  
設定を**「ログインページ変更 OFF」** (状況に応じて) にします。  
  
参考「[SiteGuard WP Plugin – WordPress プラグイン | WordPress.org](https://ja.wordpress.org/plugins/siteguard/)」

ログイン画面やコメント画面に画像認証を入れたり、その他様々なセキリティ対策をしてくれるプラグインです。  
画像認証を入れることでプログラムによるログインの試みや自動的なコメントの投稿を防ぐことができます。  
初期設定でログインページの URL を変更してさらにセキリティを強くしてもらえるのですが、URL を忘れると不便なのでここでは設定を OFF にすることにします。

## 3\. 設定

### 3.1. .htaccess

インストールディクトリの階層にもともと .htaccess を置いて書いていた場合は編集する必要はありません。

親・祖先ディレクトリにある .htaccess でリダイレクトの設定を行っていた場合、(ファイルではなく設定が) **WordPress のリダイレクトの設定で上書きされる**ので、独自にリダイレクトの設定を行いたい場合は、インストールディクトリの階層 の .htaccess に直接リダイレクト設定を追加します。  
`RewriteOptions Inherit` を使って祖先ディレクトリでの設定を引き継ぐこともできますが、リダイレクトの指定の仕方によってはディレクトリを誤ってリダイレクトされてしまうので、具体的な設定の内容を省略せずに書く方が安全です。

※注意: `BEGIN` / `END` や `START` / `END` といったコメントがある箇所は、プログラムによって自動で設定が書き込まれる領域なので、その中に手動で設定を**書かないでください**。

以下は .htaccess の例です。  
(何も考えずにコピー＆ペーストはしないでください。環境によって記述が異なります。)

##### .htaccess

```apache
# 常時 https non-www
RewriteEngine on

RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
RewriteRule ^(.*)$ https://%1/blog/$1 [R=301,L]

RewriteCond %{HTTPS} !on
RewriteRule ^(.*)$ https://%{HTTP_HOST}/blog/$1 [R=301,L]

#
# WordPress の設定
#

#SITEGUARD_PLUGIN_SETTINGS_START
#SITEGUARD_PLUGIN_SETTINGS_END

# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /blog/
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /blog/index.php [L]
</IfModule>

# END WordPress

```

### 3.2. WordPress の設定

#### 3.2.1. 一般設定

- サイトのタイトル
- キャッチフレーズ

お好みで変更。

- WordPress アドレス (URL)
- サイトアドレス (URL)

基本的にはそのままにして変更しません。  
後から `http` を `https` に変更する場合などは両方とも変更。  
インストールディレクトリの URL と表示 URL を違うものにする場合には「サイトアドレス」のみ変更。

- 週の始まり: **日曜日**

国によっては週の始まりが「月曜日」なのでデフォルトではそうなっていますが、日本では「日曜日」が主流なので「日曜日」に変更します。

#### 3.2.2. 表示設定

- RSS/Atom フィードでの各投稿の表示: **抜粋のみを表示**

RSS を用いたコピーサイトの防止。

- 検索エンジンがサイトをインデックスしないようにする

サイトデザインなどが大方決まるまでは ON (インデックスしない) 。  
公開できる状態になったら OFF (インデックスする) 。

#### 3.2.3. ディスカッション設定

用途によると思いますが、コメント機能を使用する場合には、以下のような設定すると良いと思います。

- 自分宛のメール通知
    - コメントが投稿されたとき: **OFF**
    - コメントがモデレーションのために保留されたとき: **OFF**
- コメント表示条件
    - コメントの手動承認を必須にする: **ON**
    - すでに承認されたコメントの投稿者のコメントを許可し、それ以外のコメントを承認待ちにする: **OFF**

#### 3.2.4. メディア設定

##### 3.2.4.1. 通常の設定

- サムネイルのサイズ: 150x150 → **(そのまま)**
- 中サイズ: 300x300 → **0x0**
- 大サイズ: 1024x1024 → **0x0**

デフォルト設定ではサムネイル画像が沢山生成され、サーバーの使用量がかなり増えるので、個人的に初期段階では上記のように設定しました。

0x0 に設定した項目のサムネイルは生成されません。

サムネイル画像を無効にするとサイトの表示速度に影響するので、 必要に応じて「中」と「大」のサムネイルの設定をしたり、後述のテーマのメディア設定を用いてください。

ちなみに、メディアライブラリの画像表示サイズは 150x150 (画面サイズによってはもう少し小さいサイズ) です。

##### 3.2.4.2. 隠し設定

`/wp-admin/options.php` のページで

- medium\_large\_size\_w: 768 → **0**

にします。

##### 3.2.4.3. テーマの設定

テーマの設定でもサムネイル画像が生成されていることがあります。  
この設定については「[3\. 子テーマの設定 - WordPress テーマの設定 - Blog WordPress](wordpress-テーマの設定.md#3-子テーマの設定)」を参照してください。

#### 3.2.5. パーマリンク設定

用途によると思いますが、個人的には以下のような設定が良いと思います。

- ブログなど
    - カスタム構造: **/posts/%postname%/**
    - カテゴリーベース: **category**
    - タグベース: **tag**
- その他サイト
    - カスタム構造: /%category%/%postname%/

自分は無料ブログをしていた経験があるので、「数字ベース」や「カスタム構造: /%post\_id%」の方が慣れていて、URL に投稿名が含まれることに違和感を感じたのですが、Google Search Console ヘルプ を読んでこれに決めました。  
  
参考「[シンプルな URL 構造を維持する - Search Console ヘルプ](https://support.google.com/webmasters/answer/76329?hl=ja)」  
参考「[検索エンジン最適化（SEO）スターター ガイド - Search Console ヘルプ](https://support.google.com/webmasters/answer/7451184?hl=ja)」

特に重要なのは

- なるべく ID ではなく、ひとが見て理解できる単語を用いる
- 短く簡単
- (変わらない)

だと思います。  
  
日本語を含めるのも大丈夫みたいです。  
ただし、URL が変わると面倒なので、記事のタイトルを変更したとしても投稿名は変えないべきでしょう。

`posts` と複数形にしているのは、月別アーカイブのページで `/posts/2016/07/` のように表示されるためです。  
カテゴリーベース・タグベースは初期値を指定していますが、空欄のままだと `/posts/category/カテゴリ` や `/posts/tag/タグ` のようになってしまうので、指定することで `/category/カテゴリ` や `/tag/タグ` のようにしています。

個人的にはできれば記事は `post` 、月別アーカイブは `archives` 、のように分けたいです…。  
また、検索結果の URL は `/?s=キーワード` で固定です。  
アクションフックなどで URL を書き換えることもできるようですが、ここでは保留にしておきます。

#### 3.2.6. その他設定

`/wp-admin/options.php` のページで以下のように設定します。

- use\_smilies: 1 → **0**
    - 海外の顔文字を画像に変換するオプションを無効にします。
    - 絵文字とは別に扱われます。
