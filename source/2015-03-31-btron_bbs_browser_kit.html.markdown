---
title: BTRON BBS BROWSER KIT
date: 2015-03-31 00:33:12
tags: bchan, btron, 
---
さて、私、この度、新しくSourceForge.JPにプロジェクトを作成いたしました。

<a href="http://sourceforge.jp/projects/bbk/" title="BTRON BBS BROWSER KIT" target="_blank">BTRON BBS BROWSER KIT</a>

まあこれが何かと言いますと、<a href="http://sourceforge.jp/projects/bchan/" title="bchan - BTRON ２ちゃんねるブラウザ -" target="_blank">bchan</a>の後継プロジェクトってことでしてハイ。

いや、ぶっちゃけ、現状ではbchanのソースそのまま流用してるのですけど。

なんでまた、新しいプロジェクトにしたのかっちゅーと、「<a href="http://bbs.lames.jp/2ch-api.html" title="2ch API導入についての簡易まとめ" target="_blank">2chのAPI化</a>」がきっかけです。

専ブラが2ちゃんねるのデータを取得する際の方法が変わるんですけど、単に技術的なところが変わるってだけじゃなく、利用規約まで変わりまして。許諾制になったわけですね。

で、bchanに許諾が出たかっちゅーと……一応申し込みのフォームに登録しては見たんですけど、今のところ返事がありません。

とりあえず、報告されてる事例から考えるとbchanに許諾は下りなさそうではありますが。いや、返事ないから断言はできんけど。利用条件の一部にandでつながってるだかorでつながってるんだか判然としない部分があるから、もしかしたら……とは思うんですけどね。

まあ、いずれにしても、返事が無い現時点では、APIを使うことはできないわけでしてね。

んじゃあ、このまま開発停止かっちゅーと、それももったいないと思った次第です。

世の中には、2chの互換掲示板ってのがありまして、たとえばパー速なんて有名ですし、個人でも、専ブラ対応レンタル掲示板を借りるとか、サーバー借りて<a href="http://zerochplus.sourceforge.jp/" title="ぜろちゃんねるプラス - SourceForge.JP" target="_blank">ぜろちゃんねるプラス</a>というスクリプトを使えば、専ブラ対応の掲示板を運営することできたりします。

ちゅーか、私個人で言えば、2chよりも、各種互換掲示板に出入りすることの方が多かったりします。

というわけで、互換掲示板をメインターゲットとした専ブラとして仕切りなおそう、ということで、新プロジェクトを立ち上げた次第であります。

ま、元のプロジェクトのまま開発続けるって選択肢もあったんですけどね。「2ちゃんねるブラウザ」と銘打っていながら2ちゃんねるをサポートしないってどうよ？　ってのもあるし、名前変えてもいいけど、まあ、せっかくだから元のプロジェクトも記念に残しておきたいな、とか思ったりしたものでして。

SourceForge.JP的には、開発が続けられないプロジェクトはそのまま放置してもらった方が都合がいいらしいですしね。（<a href="http://sourceforge.jp/docs/%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E7%AE%A1%E7%90%86FAQ" title="プロジェクト管理FAQ：SourceForge.JPドキュメント管理" target="_blank">プロジェクト管理FAQ</a>）。

あとついでに、ソース管理もsvnからgitに移行したり、名前とかちょっと調整したりしようかなー、とかあれこれ。

ちゅーわけで、一応3月中ぐらいを目処に公開しようと思い、プロジェクトを作って、ソースコードだけ最低限体裁整えてリポジトリに突っ込んで、見つかったバグは直してリリースしてあります。

まあ、プロジェクトWebやらドキュメントやら整備するのを、うっかり作業見積もりに入れてなかったんですけど、それは追々。

<hr>

ちなみに、「BBS BROWSER KIT」という名前はですね、bchanを利用頂いている方はご存知だと思いますが、この専ブラは単体のアプリケーションでなくて、複数のアプリケーションを組み合わせて使うように作ってあるところから来てるのですよ

いえね、構想してるアプリケーションの中には、「専ブラ」と銘打つには若干微妙なものもありまして。

実際、今bchan用テキストエディタをコツコツ作ってるのですよ。いつ完成するか分かんないけど。あと、スニペット管理ツールとか作りたいなーとか思ってたりしまして。

あと、mrubyとか組み込んで拡張機能とか実現したいなー、それならforkしたリポジトリをプロジェクト内で管理したほうが何かと都合がいいよなーとか。

「2ちゃんねるブラウザ」ってプロジェクトだと、若干入れるかどうか迷う思いつきがあるんですよ。

そんなわけで「KIT」→「<a href="http://ja.wikipedia.org/wiki/%E3%82%AD%E3%83%83%E3%83%88" title="キット：Wikipedia" target="_blank">一式、一揃い</a>」という範囲広めの言葉にした次第であります。

<hr>

ちなみに、SourceForge.JPって、プロジェクトを申請してから、運営の人手による審査を経て承認されるんですよ。

だから、申請から承認までちょっとタイムラグがあるわけでして……、

<a href="/2015/03/31/btron-bbs-browser-kit/history_first.png"><img src="/2015/03/31/btron-bbs-browser-kit/history_first.png" alt="BTRON BBS BROWSER KIT プロジェクト承認" width="411" height="291" class="alignnone size-full wp-image-223" /></a>

<span style="font-size:xx-large">早っ！？</span>

なんか夜中に申請したらノータイムで承認されました(笑)

いや、寝る前に申請しといて、運営元の営業時間始まってから朝一で承認されればいいかなー、とか思ってたらいきなり承認されて軽くビビったよ。

機械承認じゃないよなあ……？　<a href="http://sourceforge.jp/develop/whatsnew.php" title="最新動向 - SourceForge.JP" target="_blank">最新動向</a>見るとリジェクトされてるプロジェクト申請はちょくちょく見かけるし。たまたま担当者が巡回してた時間だったとかなんかな？　それにしても0分てすげえなあ。
