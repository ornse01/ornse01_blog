---

title: Debian Busterにアップデートしたついでにuimをibusに変えた
date: 2019-07-07 19:56 JST
tags: 

---

Debin Busterにアップデートしたら、なんでか端末とかに日本語入力できなくなりまして。

まあ困らない……と言いたいところですが、私の環境には日本語ファイル名とかディレクトリ名とかそれなりにありまして。

心当たりといえば、「なんやーアップデートしたら右下にツールバーが出るようになったなー。IMEかーうっとおしいな、消したろー (ﾎﾟﾁ-」ぐらいですねー。

……はい。

で、色々調べていくと、
<ul><li><a href="https://wiki.debian.org/JapaneseEnvironment/IbusSupportforBuster" title="Debian 10 / Buster でuim からIBus or fcitx に移行するためのメモ" target="_blank">Debian 10 / Buster でuim からIBus or fcitx に移行するためのメモ</a></li></ul>
というわけで、uimの開発状況は停滞しているとのこと。うちの環境に入っていたのはuimっぽい。

……正直数年前にインストールして適当に設定したっきりだからよく覚えてない。

まあそんなわけですし。uimからibasに変更しました。

<pre>
  > apt install ibus
  > apt purge uim
  > apt install ibus-mozc ibus-anthy
</pre>

mozcとanthyはもともと入れてたので省略。

で、再起動して「設定」→「地域と言語」→「入力ソース」を開いて「＋」ボタンを押して、「日本語（Mozc）」と「日本語（Anthy）」の二つを追加。

まあmozcとanthyの両方入れる意味はあんまり無いけど、入れてたからとりあえず。

とりあえず競合したりはしなさそうなのでしばらくはこのままにしてみる。

ついでに、Emacsの設定も変更。なんかibusの変換ウィンドウがEmacs上に出てくると違和感がマッハだったので。

参考にしたのはコチラ→　<a href="https://skalldan.wordpress.com/2012/05/11/emacs-ibus-mozc-%E3%81%A7%E6%97%A5%E6%9C%AC%E8%AA%9E%E5%85%A5%E5%8A%9B/" title="Emacs + iBus + Mozc で日本語入力" target="_blank">Emacs + iBus + Mozc で日本語入力</a>

<a href="https://packages.debian.org/ja/buster/emacs-mozc" title="emacs-mozc" target="_blank">emacs-mozc</a>パッケージ入れてなんやかんや。XIMを無効にするためにX側の設定が必要なのが落とし穴ですな。

可能ならibus.elを入れたい気分ではあったのだけど、Debianパッケージには無かったし見送り。

まあとりあえずDebian Busterでの日本語入力はこれでいいかなー。
