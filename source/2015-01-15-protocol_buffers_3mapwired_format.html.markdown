---
title: Protocol Buffers 3のmapのwired format
date: 2015-01-15 02:37:00
tags: protobuf_bvgsdk, 
---
さて、Protocol Buffers のversion 3で追加される新機能の一つのmap。<a href="http://ornse01.b.sourceforge.jp/2015/01/13/protobuf3%e3%81%a0%e3%81%a8%ef%bc%9f/" title="protobuf3……だと……？">このあいだ</a>、シリアライズしたときどうなるのかよくわからんと書いたけど、情報見つけました。

GoogleグループのProtocol Buffersについての話題ねー → <a href="https://groups.google.com/d/msg/protobuf/ZRpcfmeGK6s/jtevTpQE754J" title="Googleグループ Protocol Buffers：Protobuf Buffers v3.0.0-alpha-1" target="_blank">https://groups.google.com/d/msg/protobuf/ZRpcfmeGK6s/jtevTpQE754J</a>
<pre>
message A {
  map&lt;string, string&gt; values = 1;
}
</pre>
こういうふうにmapを定義すると、
<pre>
message B {
  message MapEntry {
    option map_entry = true;
    string key = 1;
    string value = 2;
  }
  repeated MapEntry values = 1;
}
</pre>
これと等価になるようにシリアライズされるとのこと。

バージョン2以前で表現可能な形でシリアライズしておくことで、map込みのデータを旧版でデシリアライズした場合でも、最低限スキップできるようになっているみたいですね。

あと気になるのは、キーに取りうるデータ型ですね。intとかstringとかのプリミティブ型だけなら実装のとき困らないんだけど、message型とか来たらどーすりゃいいのやら。そのへんイマイチ読み取れない……。

まあ、実際にv3をダウンロードして試せばいいんだけどさ。まあ、そのうち。