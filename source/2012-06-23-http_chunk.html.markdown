---
title: HTTP chunkの話
date: 2012-06-23 22:10:32+09:00
tags: 未分類, 
---
ある日、あるサイトが返してくるHTTP chunkがうまく処理できなかったのさー。

原因は返してくるHTTP chunkのデータだったー。

rfc2616の、3.6.1 Chunked Transfer Coding のこの部分
<blockquote>chunk          = chunk-size [ chunk-extension ] CRLF</blockquote>
ここんとこ、chunk-sizeとCRLFの間に、ホワイトスペース入れて返してきてたのさー。

仕様に沿ってないデータ返してるのかー、どーしよー。

……と思ったんだけど、他のウェブブラウザとかwgetコマンドとかは、きちんと取得できるのよね。

どうやらこれっぽい。rfc2616の、2.1 Augmented BNF の、implied *LWS。
<blockquote>      The grammar described by this specification is word-based. Except
      where noted otherwise, linear white space (LWS) can be included
      between any two adjacent words (token or quoted-string), and
      between adjacent words and separators, without changing the
      interpretation of a field. At least one delimiter (LWS and/or

      separators) MUST exist between any two tokens (for the definition
      of "token" below), since they would otherwise be interpreted as a
      single token.</blockquote>

トークンの間には、ホワイトスペース入ってもいいみたいねー。

……HTTPのホワイトスペースの処理って地味にメンドクサイのよね。
