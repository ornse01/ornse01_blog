---
title: mrubyとlong doubleとfmt_fp.c
date: 2015-12-10 23:58:10
tags: mruby_brightv_hello, 
---
ちゅーわけで、<a href="http://ornse01.b.osdn.me/2015/12/10/mruby-1-2-0%e5%af%be%e5%bf%9c%e4%bd%9c%e6%a5%ad/">コレ</a>について詳しく調べたので、改めて。

<h2>long double型</h2>

えー、まず<code>long double</code>とは何ぞや、と。

……実を言うと、今回初めて知った。C言語歴はそれなりに長いんですけどね……。TRON系OSの基本データ型には定義されてないから縁が無かった。

<ul><li><a href="https://ja.wikipedia.org/wiki/%E5%9B%9B%E5%80%8D%E7%B2%BE%E5%BA%A6%E6%B5%AE%E5%8B%95%E5%B0%8F%E6%95%B0%E7%82%B9%E6%95%B0" target="_blank">Wikipedia:四倍精度浮動小数点数</a></li>
<li><a href="http://www.wdic.org/w/TECH/long%20double" target="_blank">通信用語の基礎知識:long double</a></li>
<li><a href="http://www.jisc.go.jp/app/pager?%23jps.JPSH0090D:JPSO0020:/JPS/JPSO0090.jsp=&amp;RKKNP_vJISJISNO=X3010" target="_blank">日本工業標準調査会:JISX3010</a></li></ul>

と、色々調べたところ、まあ要は、double型より長い浮動小数点数で、処理系によってビット数とか構成とかが色々異なる、と。

処理系によって実装が異なる、ってことは、使い方次第では、<code>long double</code>使ってると、動いたり動かなかったりする可能性があるわけだ。

<a href="https://github.com/mruby/mruby/blob/22464fe5a0a10f2b077eaba109ce1e912e4a77de/src/fmt_fp.c" target="_blank">件のソース</a>では、プリプロセッサで、<code>long double</code>型周りの定義をチェックしていて、超漢字開発環境のgccでは、見事それに引っかかってエラーになっている、と。そういうわけだ。

で、ヘッダを調べてみたら、gcc-2.95.2の<code>long double</code>型は、<code>double</code>型と同じ構成つまり精度も同じ。……割と存在意義無いよねそれ。しかも<code>sizeof(long double)</code>すると、<code>sizeof(double)</code>よりも大きくなるようで、それが件のソースのチェックに引っかかる、と。精度同じで、使用バイト数は大きいってなんやねん……。

<h2>mrubyの浮動小数点数</h2>
で、その<a href="https://github.com/mruby/mruby/blob/22464fe5a0a10f2b077eaba109ce1e912e4a77de/src/fmt_fp.c" target="_blank">fmt_fp.c</a>が何かっちゅーと、浮動小数点数を文字列に変換する処理ねー。まあ、<code>to_s</code> とかするのに使うんやろ。

んでも、<a href="http://ornse01.b.osdn.me/2014/07/26/mruby%e3%83%93%e3%83%ab%e3%83%89%e3%82%a8%e3%83%a9%e3%83%bc%e3%81%ae%e8%a7%a3%e6%b1%ba/">以前のバージョンの時</a>はここらへんは一応コンパイル通ったわけで……。

とりあえず、状況を色々追ってみた。
<ul><li><a href="https://github.com/mruby/mruby/commits/master/src/fmt_fp.c" target="_blank">History for fmt_fp.c</a></li>
<li><a href="https://github.com/mruby/mruby/pull/2723" target="_blank">Pull request #2723: re-implement mrb_float_to_str()</a></li>
<li><a href="https://github.com/mruby/mruby/issues/2140" target="_blank">Issue #2140: mrb_float_to_str does not work with DISABLE_STDIO</a></li></ul>

まあ、どうやら、<code>DISABLE_STDIO</code>指定しても<code>stdio.h</code>が必要だったとか、結果がおかしいとか、色々あって、実装をまるごと入れ替えたようです。

オープンソースの<code>printf</code>から引っ張ってきた模様 → オリジナルは<a href="http://git.musl-libc.org/cgit/musl/tree/src/stdio/vfprintf.c" target="_blank">こちら</a>。

元が<code>vfprintf</code>だから、float型もdouble型もlong double型も一括で扱えるよう、一旦全部long double型の数に変換してからフォーマットしてるのね。

が、しかし、mrubyでの浮動小数点は、<a href="https://github.com/mruby/mruby/blob/22464fe5a0a10f2b077eaba109ce1e912e4a77de/include/mruby/value.h" target="_blank">ここ</a>で<code></code>で定義されている、<code>mrb_float</code>型ひとつしかなく、しかも、プリプロセッサで、C言語の<code>float</code>か<code>double</code>のどちらかに固定される。

そして、<a href="https://github.com/mruby/mruby/blob/22464fe5a0a10f2b077eaba109ce1e912e4a77de/src/fmt_fp.c" target="_blank">fmt_fp.c</a>が使われているところを追ってみると、<a href="https://github.com/mruby/mruby/blob/5c405dea3d3e26f62877959c75a50fbaf1ece6a4/src/numeric.c" target="_blank">numeric.c</a>と<a href="https://github.com/mruby/mruby/blob/5c405dea3d3e26f62877959c75a50fbaf1ece6a4/src/dump.c" target="_blank">dump.c</a>の2箇所だけ、なおかつ、フォーマット文字列として指定されているのは<code>"%.7g", "%.14g", "%.8e", "%.16e"</code>のたった4通り。

<code>long double</code>にわざわざ変換するのは、オーバースペックなんじゃなかろうか？

……だから、変換するコストも減らせるしlong double使うのやめようよ、みたいな路線でプルリクエスト投げたら通せないかなあ？

long double使ってるのココだけなんだよっ！！

<h2>fmt_fp.c</h2>
ってわけで、どこをどう修正しようか、ソースを眺めてるわけなんだが、<a href="https://github.com/mruby/mruby/blob/22464fe5a0a10f2b077eaba109ce1e912e4a77de/src/fmt_fp.c" target="_blank">fmt_fp.c</a>は、なかなか難解でして……。

とりあえず、プリプロセッサでガードしている理由がコメントに書いてあるんだけどさ、"overflow the stack"がどうのこうの書いてあるけど、再帰してるわけじゃないので良くわからない。ローカル変数で定義している配列の長さを超えるみたいな話かと思ってソースを読むも、配列長さのところにある28だ29だ8だ9だに詰まる……。

いや、<code>long double</code>型を全部<code>mrb_float</code>型に変えて、<code>LDBL_XXX</code>のマクロのところを、<code>MRB_FLOAT_XXX</code>みたいなマクロ定義して入れ替えて、中身は<code>MRB_USE_FLOAT</code>の有無で、<code>FLT_XXX</code>か<code>DBL_XXX</code>かに切り替えて。

ってな感じで行けるんじゃないかと思ってるんだが、プルリクエスト通すための理論武装のためには、「とりあえず入れ替えてみたらうまく行っちゃった」じゃなくて「ソースコードがこれこれこうなってるから大丈夫だ」って感じで理論武装しておきたいわけで……が、しかし、かなり難しいなあコレ。

あとね、あとねー、テストどうしよう？

使ってるフォーマット文字列がほぼ固定も同然なので、mrubyのテストコードで色んなパターン試すってことが出来ないじゃん……。

プリプロセッサ系の動作確認は、環境買えてビルドして動作確認もしておきたい……となると、gccやclangはいいとして、64bit OSと32bit OSも試して？　あとMSVCも用意せにゃならんよね……、そしてMAC持ってない。

まあ、floatとdoubleならそうそう変わらないとは思うけど。

うーむ。仮に通したとして、その後にバグが出てきたときに、リバートされたりしないようにしておきたいわけで……、現状のコードだとどうなんだろうなあ。

……まあ、とりあえず、一回、<code>mrb_float</code>化は試しにやってみるかなあ？