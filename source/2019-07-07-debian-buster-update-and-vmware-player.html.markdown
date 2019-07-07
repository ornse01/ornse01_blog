---

title: Debian Busterへのアップデート後のVMware WorkStation Player起動
date: 2019-07-07 16:00 JST
tags: 

---

<a href="https://www.debian.org/News/2019/20190706" title="Debian 10 buster released" target="_blank">2019/07/06にDebian BusterがStableになりました</a>ねー。

ということで、さっそく私のPCもBusterにアップデートしました。

で、概ね問題なくアップデートできました。

……まあ、問題ゼロというわけではなく<a href="https://packages.debian.org/ja/buster/gnome-accessibility-themes" title="gnome-accessibility-themes" target="_blank">gnome-accessibility-themes</a>のdebファイルが403 Forbiddenを返すって問題が起きましたが。とりあえずスキップさせて進めても大丈夫だったのでいいことにしましょう。

で、Debian自体はアップデートできたのですが、VMware Workstation Player起動時に問題が発生したのでそのメモです。15.1.0です。

まあDebianホストでVMware Workstation Playerを利用している方はご存知でしょうが、ホストのLinuxカーネルがアップデートされると、その度にVMwareもアップデート(?)が走ります。カーネルのバージョンに合わせてモジュールも再ビルドしてるっぽいですね。

というわけで、Stretch → Busterにアップデートしたら当然その再ビルドが走るわけですが……それがうまく動かなくて困ったー、ってわけですねー。


で、結論から言いますと、端末から以下のコマンドを実行して、VMwareの更新を走らせます。
<pre>
  > xhost +si:localuser:root
  > vmplayer
</pre>

参考資料はコチラ
<ul>
  <li><a href="https://www.youtube.com/watch?v=1xIvppquqco" title="[FIX] Linux OS: Cannot open display GUI for Wayland" target="_blank">[FIX] Linux OS: Cannot open display GUI for Wayland</a></li>
  <li><a href="https://wiki.archlinux.jp/index.php/Root_%E3%81%A7_X_%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E8%B5%B7%E5%8B%95" title="archlinux wiki:Root で X アプリケーションを起動" target="_blank">Root で X アプリケーションを起動</a></li>
</ul>

Debian Busterからデスクトップ環境のデフォルトがWaylandに変わったせいで、root権限でGUIアプリケーションを動かす方法が変わったっぽいですね。

以前は特に意識しなくても大丈夫だったので元々どういう仕組みだったのかはサッパリですが、Wayland環境下のホストにVMwareを入れようとするとこういうのが必要と思われます。

多分、今後のDebianのアップデートでカーネルのバージョンが変わる度にこれが必要っぽい雰囲気ですなー。

そもそもroot権限でGUIアプリケーションを動かすなって話しっぽいですが、VMWareのインストール・アップデートには必要っぽいわけで。。。まあなんとかして欲しいところ。

なんにしても、VMwareが無事起動できたので、超漢字Vもバッチリです。
