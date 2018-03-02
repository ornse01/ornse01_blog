---
title: mrubyのbuild_config.rb for 超漢字
date: 2014-07-22 02:25:55
tags: mruby_brightv_hello, 
---
mrubyでクロスコンパイルしようと思ったら、build_config.rbってファイルを適切に記述して、コンパイラやらなんやらを指定しなければいけないんですなー。

ちゅーわけでこれ→<a title="mruby-brightv-hello src/build_config.rb" href="http://sourceforge.jp/users/ornse01/pf/mruby_brightv_hello/scm/blobs/45103246e7f332c6e611ee7ce0340219508d4d37/src/build_config.rb" target="_blank">build_config.rb</a>

まー、上の方はホスト環境でデフォルトのまんまなんで関係ないとして、超漢字用にあれこれしてみたのは下の方。

肝心なところだけ抜き出してみたのが<a href="http://sourceforge.jp/users/ornse01/pastebin/1020" title="コピペ: mruby build_config.rb for B-right/V (experimental)" target="_blank">こちら</a>。

まあ、クロスコンパイルの方法はググれば出てくるから、置いといて。

まず二行目のところに'pcat'って指定してあるよねー。これがmrubyをビルドしたときのオブジェクトファイルやらなんやらの置き場のディレクトリ名に影響すると。

とりあえず、超漢字開発環境で使ってるターゲット名に合わせてみた。……まあホスト環境もPC/ATだから若干もにょるけど、とりあえず置いといて。

C compiler settings と Archiver settings もまあ、説明はいらんでしょう。超漢字開発環境にある設定をコピペってきました。<a href="http://ornse01.b.sourceforge.jp/2014/07/19/%e8%b6%85%e6%bc%a2%e5%ad%97%e9%96%8b%e7%99%ba%e7%92%b0%e5%a2%83%e3%81%a7rake/" title="超漢字開発環境でrake">Rakefile側</a>にも同じ設定書いてあるのはなんとかしたいなあと思いつつ。

んで

[ruby]conf.build_mrbtest_lib_only

@bins = [][/ruby]

まあ詳しいことは<a href="https://github.com/mruby/mruby/blob/e43341b4607a2596ba2b6542acc8406912cf289a/doc/compile/README.md" title="mruby/mruby  mruby/doc/compile/README.md" target="_blank">ドキュメント</a>見てもらうとして。

mrubyのビルドを実行するとライブラリのほかいくつかの実行ファイルも作られるわけでして。

んですけど、BTRONではエントリーポイントがMAINですから、mainをエントリーにした実行ファイルを作りにいかれると色々困るのですよ。

それをどーにかするのがこの部分。

上の方がテスト用のmrbtestを作らない設定、下の方がmrbcコマンドを作らない設定ですな。……mrbcコマンド作らない設定はもう少しなんとかならんのかっちゅー気もするけど。

あ、これらは、クロスコンパイル用の設定に書いてますので、ホスト環境ではどちらも作られます。

とりあえず、こんな感じでビルド設定ができましたー。