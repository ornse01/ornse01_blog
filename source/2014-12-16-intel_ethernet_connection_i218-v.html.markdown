---
title: Intel® Ethernet Connection I218-V
date: 2014-12-16 00:00:55
tags: 未分類, 
---
さて、何で突然<a href="http://www.intel.co.jp/content/www/jp/ja/embedded/products/networking/ethernet-connection-i218-family.html" title="インテル® イーサネット・コネクション I218 シリーズ" target="_blank">Intelのイーサネットコントローラ</a>なのかっちゅーとね、新しいパソコンを買おうと思ってましてね。

んで、候補に入ったやつがこれ積んでたんですよ。

超漢字がこれに対応してるかっちゅーと、うーん……。

ちゅーわけで、自力でドライバ書こうと思って色々資料を探していたわけです。

で、あるにはあったんですけど……→<a href="http://www.intel.co.jp/content/www/jp/ja/embedded/products/networking/ethernet-connection-i218-gbe-datasheet.html" title="Intel® Ethernet Connection I218: Datasheet" target="_blank">Intel® Ethernet Connection I218: Datasheet</a>

これ、送受信のバッファのこと書いてなくね？

うーん？

んで、linuxのソース眺めたり、あちこちさまよったりして見つけたのがこれ→<a href="http://www.intel.com/content/www/us/en/embedded/products/networking/82574l-gbe-controller-datasheet.html" title="Intel® 82574 Gigabit Ethernet Controller Family: Datasheet" target="_blank">Intel® 82574 Gigabit Ethernet Controller Family: Datasheet</a>

こいつは送受信のディスクリプタのこと書いてあるから行けそう。

型番は違うけど、I218は、I82579と互換性があるとか書いてあるから、82579と82574なら、まあ近そうじゃね？

linuxのソース見ると、大体そのへんの型番のは一つのソースにまとまってるっぽいし、レジスタのアドレスの名前や値とかも一致するし。

まあ、そのものずばりの型番が見当たらないのが、若干不安だが。……まあlinux kernelの3.15からI218Vは対応してるって話だから、まあ大丈夫なんじゃないだろうか？
