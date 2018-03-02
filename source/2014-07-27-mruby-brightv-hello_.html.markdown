---
title: mruby-brightv-hello ビルドプロセス
date: 2014-07-27 20:41:06
tags: mruby_brightv_hello, 
---
<a href="https://github.com/mruby/mruby/tree/e43341b4607a2596ba2b6542acc8406912cf289a/doc/compile#build-process" title="mruby/mruby mruby/doc/compile/Readme.md build-process" target="_blank">このドキュメント</a>を参考にしつつ、<a href="http://sourceforge.jp/users/ornse01/pf/mruby_brightv_hello/wiki/FrontPage" title="作業部屋 mruby-brightv-hello" target="_blank">mruby-brightv-hello</a>のビルドプロセスを説明してみるでござるの巻。

リビジョンは 9d0e873af7a07fdc75bf23c321b8f120981e8d1b ねー。

<h2>ディレクトリ構成</h2>
<pre>
+- src
|  |
|  +- mruby          &lt;- mruby ソース
|  |
|  +- Rakefile        &lt;- ビルド設定
|  | 
|  +- hello.c, posix.c, driver.c  &lt;- BTRONアプリケーションソース
|  |
|  +- posix_inlcude   &lt;- ヘッダ
|  |
|  +- mrbgems_port    &lt;- mrbgems 
|
+- pcat
   +- build
      |
      +-  host
      |   |
      |   +- bin           &lt;- Native Binaries
      |   |
      |   +- lib           &lt;- Native Libraries
      |   |
      |   +- mrblib
      |   |
      |   +- src
      |   |
      |   +- test          &lt;- Native mrbtest tool
      |   |
      |   +- tools
      |      |
      |      +- mirb
      |      |
      |      +- mrbc
      |      |
      |      +- mruby
      +- pcat
         |
         +- bin            &lt;- Cross-compiled Binaries
         |
         +- lib            &lt;- Cross-compiled Libraries
         |
         +- mrblib
         |
         +- src
         |
         +- test           &lt;- Cross-compiled mrbtest tool
</pre>
<h2>ビルド順序</h2>
<ul>
<li>mruby本体のホスト環境ビルドを開始
  <ul>
    <li>src/mruby/src 以下のファイルをコンパイル、オブジェクトファイルは pcat/mruby/host/src以下に置かれる</li>
    <li>src/mruby/src/parse.y からパーサを生成、結果は pcat/mruby/host/src/y.tab.c</li>
    <li>pcat/mruby/host/src/y.tab.c をコンパイルし pcat/mruby/host/src/y.tab.o を生成</li>
    <li>オブジェクトファイルから pcat/mruby/host/lib/libmruby_core.a を生成(C only)</li>
    <li>src/mruby/tools/mrbc/mrbc.c をコンパイルし  pcat/mruby/host/lib/libmruby_core.a とリンクして pcat/mruby/host/bin/mrbc を生成</li>
    <li>src/mruby/mrblib 以下の *.rb ファイル全てを、pcat/mruby/host/bin/mrbc を使ってコンパイルし、pcat/mruby/host/mrblib/mrblib.c を生成</li>
    <li>pcat/mruby/host/mrblib/mrblib.c をコンパイルして pcat/mruby/host/mrblib/mrblib.o を生成</li>
    <li>mrbgemsをコンパイルし、pcat/mruby/host/mrbgems 以下にオブジェクトファイルを生成</li>
    <li>全てのオブジェクトファイルから pcat/mruby/host/lib/libmruby.a を生成(C and Ruby)</li>
    <li>src/mruby/mrbgems/mruby-bin-mruby/tool/mruby/mruby.c をコンパイルし、pcat/mruby/host/lib/libmruby.a とリンクして pcat/mruby/host/bin/mruby を生成<li>
    <li>src/mruby/mrbgems/mruby-bin-mirb/tool/mruby/mruby.c をコンパイルし、pcat/mruby/host/lib/libmruby.a とリンクして pcat/mruby/host/bin/mirb を生成<li>
  </ul>
</li>
<li>mruby本体のクロスビルドを開始
  <ul>
    <li>src/mruby/src 以下のファイルをクロスコンパイル、オブジェクトファイルは pcat/mruby/pcat/src以下に置かれる</li>
    <li>src/mruby/src/parse.y からパーサを生成、結果は pcat/mruby/pcat/src/y.tab.c</li>
    <li>pcat/mruby/pcat/src/y.tab.c をコンパイルし pcat/mruby/pcat/src/y.tab.o を生成</li>
    <li>オブジェクトファイルから pcat/mruby/pcat/lib/libmruby_core.a を生成(C only)</li>
    <li>src/mruby/mrblib 以下の *.rb ファイル全てを、pcat/mruby/host/bin/mrbc を使ってコンパイルし、pcat/mruby/pcat/mrblib/mrblib.c を生成</li>
    <li>pcat/mruby/pcat/mrblib/mrblib.c をコンパイルして pcat/mruby/pcat/mrblib/mrblib.o を生成</li>
    <li>mrbgemsをコンパイルし、pcat/mruby/pcat/mrbgems 以下にオブジェクトファイルを生成</li>
    <li>全てのオブジェクトファイルから pcat/mruby/pcat/lib/libmruby.a を生成(C and Ruby)</li>
  </ul>
</li>
<li>src/hello.c, src/posix.c をコンパイルし、pcat/hello.o, src/posix.o を生成</li>
<li>オブジェクトファイルとpcat/mruby/pcat/lib/libmruby.a をリンクし hello.oを生成</li></ul>

まあ大体こんな感じですかね。細かい順序は依存関係の解決順序で変わるかもしんない？

<a href="http://ornse01.b.sourceforge.jp/2014/07/27/mruby%e3%81%ae%e3%83%86%e3%82%b9%e3%83%88/" title="mrubyのテスト">テストのやつ</a>は参考にしたやつに書いてなかったから省略したけど、多分 libmruby.a 生成後あたりなんじゃないかなー？