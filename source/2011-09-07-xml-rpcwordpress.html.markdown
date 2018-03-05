---
title: XML-RPCでWordpressのブログにアクセスしてみた
date: 2011-09-07 18:10:46+09:00
tags: 未分類, 
---
<div>試してみた結果のメモ。</div><br />
<div>XMLファイルを作成し、wgetでPOSTを送信、って感じで確認。生データがどんなのになるか見たかったのです。</div><br />
<div>
<ol>
	<li>まずは、ログインして、「設定」→「投稿設定」→「リモート投稿」→「XML-RPC」にチェックを入れて有効にする</li>
	<li>以下のファイル ( blogger.getUserBlogs.xml )を作成
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodCall&gt;
  &lt;methodName&gt;blogger.getUsersBlogs&lt;/methodName&gt;
  &lt;params&gt;
    &lt;param&gt;  &lt;value&gt;XXX&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;ornse01&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;&lt;!-- <span style="color: #ff0000">ブログのログインパスワード</span> --&gt;&lt;/value&gt;  &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodCall&gt;</pre>
一つ目の引数はWordPressの場合はなんでもいいらしい。二つ目はユーザー名。三つ目はパスワード。

んで、wget
<pre>wget --post-file=blogger.getUserBlogs.xml http://ornse01.b.sourceforge.jp/xmlrpc.php</pre>
</li>
	<li>そのレスポンス
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodResponse&gt;
  &lt;params&gt;
    &lt;param&gt;
      &lt;value&gt;
      &lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;isAdmin&lt;/name&gt;&lt;value&gt;&lt;boolean&gt;1&lt;/boolean&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;url&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;blogid&lt;/name&gt;&lt;value&gt;&lt;string&gt;404&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;blogName&lt;/name&gt;&lt;value&gt;&lt;string&gt;ornse01のblog&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;xmlrpc&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/xmlrpc.php&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;
      &lt;/value&gt;
    &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodResponse&gt;</pre>
同一ユーザーが複数のブログを持ってるときは複数返ってくるらしい。</li>
	<li>このレスポンスから、目的のblogIDを取得。この場合は404</li>
	<li><address>blogIDを使って、metaWeblog.getRecentPosts を呼び出し、最新の記事リストを取得する。
以下のファイル( metaWeblog.getRecentPost.xml )を作成し、</address>
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodCall&gt;
  &lt;methodName&gt;metaWeblog.getRecentPosts&lt;/methodName&gt;
  &lt;params&gt;
    &lt;param&gt;  &lt;value&gt;404&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;ornse01&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;&lt;!-- <span style="color: #ff0000">ブログのログインパスワード</span> --&gt;&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;&lt;i4&gt;10&lt;/i4&gt;&lt;/value&gt;  &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodCall&gt;</pre>
wget
<pre>wget --post-file=metaWeblog.getRecentPosts.xml http://ornse01.b.sourceforge.jp/xmlrpc.php</pre>
</li>
	<li>そのレスポンス。
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodResponse&gt;
  &lt;params&gt;
    &lt;param&gt;
      &lt;value&gt;
      &lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;dateCreated&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T15:31:51&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;userid&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;postid&lt;/name&gt;&lt;value&gt;&lt;string&gt;12&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;description&lt;/name&gt;&lt;value&gt;&lt;string&gt;BlogのAPIの資料
&amp;lt;ul&amp;gt;
	&amp;lt;li&amp;gt;&amp;lt;a href=&amp;quot;http://blog.syuhari.jp/archives/1373&amp;quot;&amp;gt;[WordPress] XML-RPC を使用する方法&amp;lt;/a&amp;gt;&amp;lt;/li&amp;gt;
	&amp;lt;li&amp;gt;&amp;lt;a href=&amp;quot;http://bpm.cocolog-nifty.com/d/cat36016795/index.html&amp;quot;&amp;gt;ブログサービスの投稿方式・エンドポイント&amp;lt;/a&amp;gt;&amp;lt;/li&amp;gt;
	&amp;lt;li&amp;gt;&amp;lt;a href=&amp;quot;http://sozai.7gates.net/blog/docs/blogapi/&amp;quot;&amp;gt;BlogAPIのエンドポイント集&amp;lt;/a&amp;gt;&amp;lt;/li&amp;gt;
&amp;lt;/ul&amp;gt;
とりあえず、このあたりの資料があればいけるかな？&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;title&lt;/name&gt;&lt;value&gt;&lt;string&gt;BlogAPIの資料&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;link&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/blogapi%e3%81%ae%e8%b3%87%e6%96%99/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;permaLink&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/blogapi%e3%81%ae%e8%b3%87%e6%96%99/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;categories&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;string&gt;未分類&lt;/string&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_excerpt&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_text_more&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_comments&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_pings&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_keywords&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_slug&lt;/name&gt;&lt;value&gt;&lt;string&gt;blogapi%e3%81%ae%e8%b3%87%e6%96%99&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_password&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_id&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_display_name&lt;/name&gt;&lt;value&gt;&lt;string&gt;ornse01&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;date_created_gmt&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T06:31:51&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;post_status&lt;/name&gt;&lt;value&gt;&lt;string&gt;publish&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;custom_fields&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;10&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_last&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;11&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_lock&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;1312266713:405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_post_format&lt;/name&gt;&lt;value&gt;&lt;string&gt;standard&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;dateCreated&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T15:19:57&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;userid&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;postid&lt;/name&gt;&lt;value&gt;&lt;string&gt;6&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;description&lt;/name&gt;&lt;value&gt;&lt;string&gt;ちなみに、今はbchanという超漢字向け2ちゃんねるブラウザを作っています。　&amp;lt;a href=&amp;quot;http://bchan.sourceforge.jp/&amp;quot;&amp;gt;http://bchan.sourceforge.jp/&amp;lt;/a&amp;gt;

&amp;lt;a href=&amp;quot;http://ornse01.b.sourceforge.jp/files/2011/08/install.png&amp;quot;&amp;gt;&amp;lt;img class=&amp;quot;alignnone size-full wp-image-8&amp;quot; src=&amp;quot;http://ornse01.b.sourceforge.jp/files/2011/08/install.png&amp;quot; alt=&amp;quot;&amp;quot; width=&amp;quot;771&amp;quot; height=&amp;quot;298&amp;quot; /&amp;gt;&amp;lt;/a&amp;gt;
とりあえず、インストール法の画像とか貼って見る。（よさげな画像が無かった……）
まあブログツールとか作ってないで、こっちを先に作りこめって話ではありますが……。&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;title&lt;/name&gt;&lt;value&gt;&lt;string&gt;bchan&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;link&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/bchan/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;permaLink&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/bchan/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;categories&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;string&gt;未分類&lt;/string&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_excerpt&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_text_more&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_comments&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_pings&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_keywords&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_slug&lt;/name&gt;&lt;value&gt;&lt;string&gt;bchan&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_password&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_id&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_display_name&lt;/name&gt;&lt;value&gt;&lt;string&gt;ornse01&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;date_created_gmt&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T06:19:57&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;post_status&lt;/name&gt;&lt;value&gt;&lt;string&gt;publish&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;custom_fields&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;5&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_last&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;6&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_lock&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;1312265999:405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_post_format&lt;/name&gt;&lt;value&gt;&lt;string&gt;standard&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;dateCreated&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T05:50:42&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;userid&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;postid&lt;/name&gt;&lt;value&gt;&lt;string&gt;1&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;description&lt;/name&gt;&lt;value&gt;&lt;string&gt;ブログ投稿ツールでも作ってみようかなー？　と思って&amp;lt;a href=&amp;quot;http://b.sourceforge.jp/&amp;quot;&amp;gt;SourceForge.JP Blog&amp;lt;/a&amp;gt;を始めてみました。

まあどこか他の無料ブログサイトでもいいんでしょうけど、テスト用に開設したあと、消すと同じアカウントで再開設できないところが多かったので、結局ここにしました。

超漢字から投稿するツールさえ作れれば、あとはプログラミングのメモにでも使おうと思います。&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;title&lt;/name&gt;&lt;value&gt;&lt;string&gt;Hello world!&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;link&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/hello-world/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;permaLink&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/hello-world/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;categories&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;string&gt;未分類&lt;/string&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_excerpt&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_text_more&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_comments&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_pings&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_keywords&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_slug&lt;/name&gt;&lt;value&gt;&lt;string&gt;hello-world&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_password&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_id&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_display_name&lt;/name&gt;&lt;value&gt;&lt;string&gt;ornse01&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;date_created_gmt&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T05:50:42&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;post_status&lt;/name&gt;&lt;value&gt;&lt;string&gt;publish&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;custom_fields&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;3&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_last&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;2&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_lock&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;1312265200:405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_post_format&lt;/name&gt;&lt;value&gt;&lt;string&gt;standard&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;
      &lt;/value&gt;
    &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodResponse&gt;</pre>
</li>
	<li>一覧から、取得したい記事のIDを選択。以下の例では6
ファイル( metaWeblog.getPost.xml )を作成
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodCall&gt;
  &lt;methodName&gt;metaWeblog.getPost&lt;/methodName&gt;
  &lt;params&gt;
    &lt;param&gt;  &lt;value&gt;6&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;ornse01&lt;/value&gt;  &lt;/param&gt;
    &lt;param&gt;  &lt;value&gt;&lt;!-- <span style="color: #ff0000">ブログのログインパスワード</span> --&gt;&lt;/value&gt;  &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodCall&gt;</pre>
んで、wget
<pre>wget --post-file=metaWeblog.getPost.xml http://ornse01.b.sourceforge.jp/xmlrpc.php</pre>
</li>
	<li>そのレスポンス
<pre>&lt;?xml version="1.0"?&gt;
&lt;methodResponse&gt;
  &lt;params&gt;
    &lt;param&gt;
      &lt;value&gt;
      &lt;struct&gt;
  &lt;member&gt;&lt;name&gt;dateCreated&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T15:19:57&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;userid&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;postid&lt;/name&gt;&lt;value&gt;&lt;int&gt;6&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;description&lt;/name&gt;&lt;value&gt;&lt;string&gt;ちなみに、今はbchanという超漢字向け2ちゃんねるブラウザを作っています。　&amp;lt;a href=&amp;quot;http://bchan.sourceforge.jp/&amp;quot;&amp;gt;http://bchan.sourceforge.jp/&amp;lt;/a&amp;gt;

&amp;lt;a href=&amp;quot;http://ornse01.b.sourceforge.jp/files/2011/08/install.png&amp;quot;&amp;gt;&amp;lt;img class=&amp;quot;alignnone size-full wp-image-8&amp;quot; src=&amp;quot;http://ornse01.b.sourceforge.jp/files/2011/08/install.png&amp;quot; alt=&amp;quot;&amp;quot; width=&amp;quot;771&amp;quot; height=&amp;quot;298&amp;quot; /&amp;gt;&amp;lt;/a&amp;gt;
とりあえず、インストール法の画像とか貼って見る。（よさげな画像が無かった……）
まあブログツールとか作ってないで、こっちを先に作りこめって話ではありますが……。&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;title&lt;/name&gt;&lt;value&gt;&lt;string&gt;bchan&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;link&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/bchan/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;permaLink&lt;/name&gt;&lt;value&gt;&lt;string&gt;http://ornse01.b.sourceforge.jp/2011/08/02/bchan/&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;categories&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;string&gt;未分類&lt;/string&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_excerpt&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_text_more&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_comments&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_allow_pings&lt;/name&gt;&lt;value&gt;&lt;int&gt;1&lt;/int&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;mt_keywords&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_slug&lt;/name&gt;&lt;value&gt;&lt;string&gt;bchan&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_password&lt;/name&gt;&lt;value&gt;&lt;string&gt;&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_id&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_author_display_name&lt;/name&gt;&lt;value&gt;&lt;string&gt;ornse01&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;date_created_gmt&lt;/name&gt;&lt;value&gt;&lt;dateTime.iso8601&gt;20110802T06:19:57&lt;/dateTime.iso8601&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;post_status&lt;/name&gt;&lt;value&gt;&lt;string&gt;publish&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;custom_fields&lt;/name&gt;&lt;value&gt;&lt;array&gt;&lt;data&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;5&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_last&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
  &lt;value&gt;&lt;struct&gt;
  &lt;member&gt;&lt;name&gt;id&lt;/name&gt;&lt;value&gt;&lt;string&gt;6&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;key&lt;/name&gt;&lt;value&gt;&lt;string&gt;_edit_lock&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;value&lt;/name&gt;&lt;value&gt;&lt;string&gt;1312265999:405&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;&lt;/value&gt;
&lt;/data&gt;&lt;/array&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;wp_post_format&lt;/name&gt;&lt;value&gt;&lt;string&gt;standard&lt;/string&gt;&lt;/value&gt;&lt;/member&gt;
  &lt;member&gt;&lt;name&gt;sticky&lt;/name&gt;&lt;value&gt;&lt;boolean&gt;0&lt;/boolean&gt;&lt;/value&gt;&lt;/member&gt;
&lt;/struct&gt;
      &lt;/value&gt;
    &lt;/param&gt;
  &lt;/params&gt;
&lt;/methodResponse&gt;</pre>
</li>
</ol>
で、まあ気がついたこととかは以下
<ul>
	<li>ブログ本文内のタグは、文字参照でエスケープしてた。</li>
	<li>metaWeblog.getRecentPostsで結構色々な情報が取得できるのね。metaWeblog.getPostは要らないか？</li>
	<li>記事IDが結構飛ぶね。理由は不明。記事の削除とかはまだしてないし……。</li>
	<li>投稿は試してない。</li>
</ul>
思ったより簡単でした。このあたりの確認のために捨てアカ作って捨てブログ作ろうとか、一時期考えてたけど、そこまでしなくてよかった。

</div>
