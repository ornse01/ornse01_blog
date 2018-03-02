---
title: multirust-rs
date: 2015-12-09 01:17:27
tags: 未分類, 
---
<a href="https://github.com/Diggsey/multirust-rs" target="_blank">Diggsey/multirust-rs</a>なるものを見つけたー。

なんか、<a href="https://github.com/Diggsey/multirust-rs#toolchain-names" target="_blank">任意のToolchainがインストールできる</a>っぽい？

つーことで軽く調べてみたー。

<ul><li><a href="https://github.com/brson/multirust" target="_blank">brson/multirust</a>をRustで実装したものだそうで</li>
<li>Linuxだけじゃなくて、WindowsでもMacでも使えるってー</li>
<li>インストーラーがあるぞ。</li>
<li>インストーラーじゃなくて、Cargo使ってもインストールできるっぽい</li>
<li>インストーラー実行してみたら、既にRustがインストールされてると警告がでたり、<code>.profile</code>でPATH上書くか確認してくるー。</li>
<li>で、<code>$(HOME)/.multirust/bin</code>に実行ファイルが置かれて、そこにPATHを通して使うそうだ。<a href="https://github.com/brson/multirust" target="_blank">brson/multirust</a>は<code>/usr/local/bin/</code>下にインストールされる。</li>
<li>……その気になればdirenvとかで、<a href="https://github.com/brson/multirust" target="_blank">brson/multirust</a>と共存できる？　してどーすんだって話だが</li>
<li>アンインストーラーは無いっぽい？　まあ、<code>$(HOME)/.multirust</code>以下消せばいいだけだからいいのか</li>
<li>Windowsだとレジストリ書き換えるみたいだから、そこも気をつけないと。</li>
<li>あとは、大体<a href="https://github.com/brson/multirust" target="_blank">brson/multirust</a>と同じ感じ</li>
<li>試しに、64bitのLinuxからで、<code>i686-gnu-nightly</code>ってtoolchainを指定してみたのよ。cpuとabiだけ指定したら、OSは補完してくれるのかなって。</li>
<li>そしたら、まあ当然実行ファイルとかライブラリとかは<code>$(HOME)/.multirust</code>以下に配置されるんだけど、ディレクトリ名は<code>i686-gnu-nightly</code>だったよ。んで、その下辿って行ったら、<code>i686-unknown-linux-gnu</code>ってディレクトリがあったよ</li>
<li>で、どうも、こりゃ<code>rustc</code>や<code>cargo</code>なんかの実行ファイル自体が32bit版のものをダウンロードして配置してるのね。libz.so.1なんかの32bit版の共有ライブラリが無いってエラーが出てきたんで、インストールしたら実行できた。</li>
<li>まあ、そりゃそうか……？</li></ul>

<code>~/.multirust</code>以下のディレクトリ構成は、もうちょっと何段階かあったと思うけど、忘れちった。

ちょっと、求めてるものとは違ったねえ。64bit版の実行ファイルに、32bit版のライブラリを配置してくれればなあ。

……自分で言うのも何だけど、かなりニッチな機能欲しがってるとは思う。自分で実装したとしてプルリクエスト投げたとして採用されるかねえ？

そもそも、この要求ってワークアラウンドのための物だしなあ……。正式な機能として要求するのはちょっと違うかなあ。

Rustクロスコンパイラを用意するなら、ソースに手を入れてカスタマイズして自分でビルドってのが正道な気がしているし。面倒だけど。ってか、どんな順番で何をビルドしてるのかいまいち把握しきれていないけど。

あ、別にmultirust-rsが悪いって話では無いです。

多分、multirust本家じゃなくmultirust-rsを先に知ってこっちインストールしてたらそのまま使ってただろう、ってぐらい遜色無さそうです。まあ、multirust本家から積極的に乗り換える理由も無いかなー？　って感じ。