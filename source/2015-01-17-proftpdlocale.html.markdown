---
title: ProFTPDのlocale設定メモ
date: 2015-01-17 14:48:27
tags: 未分類, 
---
なんで突然ProFTPDかと言いますとですね、私ですね、超漢字のアプリケーション開発で、linuxでコーディング＆ビルドしてるんですけどね、超漢字側に実行ファイルとか持っていくのにftp使ってるんですよ。

んで、ftpサーバーにずっとProFTPD使ってるんですね。

今まではそれで特に問題はなかったんですけど、最近、新しいパソコン買ってDebianのjessie入れて同じように環境を構築してみたら、超漢字でfgetしたときにメッセージが文字化けするようになったんですよ。過去のバージョンのDebianだとそんなこと無かったんですけどね。

メッセージだけなんで実害は無いんですけど、化けたままだと気持ち悪いですよね。

というわけで、設定の修正のメモです。

ポイントはlocaleの設定ですな。Debianのインストール時に日本語を選択してたんですけど、そうするとlocaleがja_JP.utf8になるんですな。そうするとProFTPDのメッセージもUTF-8の日本語になって、超漢字のfgetでは文字化けする、というわけですね。

fgetコマンドにエンコーディングを指定するオプションは見当たらないし、仮にあったとしても毎回指定するのは面倒だし、en_USにしてしまおうと思います。

<h2>localeの追加</h2>
[shell]
% locale -a
[/shell]
とコマンドを実行すると、設定可能なlocaleの一覧が表示されますが、なんか私の環境だとen_USがありませんでした。

なので追加します。
[shell]
% dpkg-reconfigure locales
[/shell]
とコマンドを打ちまして、

で、この画面のところで、インストールしたいlocaleにチェックを入れます。今回入れたいのはen_USなのでこんな感じかな？
<a href="/2015/01/17/proftpdlocale/dpkg_reconfigure_locale.png"><img src="/2015/01/17/proftpdlocale/dpkg_reconfigure_locale.png" alt="dpkg_reconfigure_locale" width="641" height="386" class="alignnone size-full wp-image-210" /></a>
en_US.UTF-8は要らんと思うけどまあ一応。

次の画面でシステムのlocaleを指定するんですけど、今回は変更しないので省略。

参考：<a href="http://setting-tool.net/debian-locale-japanease" title="Debianでのロケール変更（日本語環境に変更する）" target="_blank">Debianでのロケール変更（日本語環境に変更する）</a>

<h2>ProFTPDのlocale指定</h2>
んで次はProFTPDの設定。

<code>/etc/proftpd/conf.d/lang</code>ってファイルを作りまして中身はこんな感じ。
<pre>&lt;IfModule mod_lang.c&gt;
    LangDefault en_US
&lt;/IfModule&gt;</pre>

<code>/etc/proftpd/proftp.conf</code>眺めてたら、<code>/etc/proftpd/conf.d/</code>以下に置かれているファイルは自動的に読み込まれるようになってたので、こうしてみました。

もしかしたら、ディストリビューションやらバージョンやらなんやらで、設定書くべき場所が変わるかもしれないけど、そのへんは調べてません。

参考：<a href="http://iuc.didit.jp/blog/lab/2011/09/proftpd-langdefault.html" title="Webアプリケーション開発ラボ by NPO情報活用センター：ProFTPDのメッセージの言語を変更する LangDefault" target="_blank">ProFTPDのメッセージの言語を変更する LangDefault</a>

<h2>xinetd再起動</h2>
設定を反映させるために再起動
[shell]
% /etc/init.d/proftpd stop

% /etc/init.d/xinetd restart
[/shell]

私は、とりあえずxinetd経由でProFTPDを動かしているので、xinetdごと再起動です。スタンドアローンで動かしていたら再起動の方法は違うんだろうけど、試してないんで省略。

参考：<a href="http://www.turbolinux.com/support/document/knowledge/466.html" title="turbolinuxサポートドキュメント：ProFTPD を xinetd を介して起動する" target="_blank">ProFTPD を xinetd を介して起動する</a>
