---
layout: post
title: Let it crashについて
categories: [Programming]
---
Elixir/Erlangを勉強してなかった時、c++の例外処理について、例外の安全性の確保が難しいことと例外処理を入れるとコードが理解しにくくなることを認識して、もっと良い手段がないかを調べたら、let it crashという言葉を知り、誤解のままc++のプロジェクト（デスクトップアプリ）で使ってしまいました。例えば、エラー処理するべきところに変なassertを入れて、ユーザーから予想外の操作がされたら思い切り落ちってしまいます。そのあとassertするコードを排除したが、let it crashの概念がずっと誤解したままでした。ElixirでOTPを勉強したら予約let it crashについて少し理解できたので、メモを残ります。

# let it crashとは何か？
Erlangでは、不明なエラーが起こったとき、防御的なプログラミングをすることではなく、processをcrashさせる。crashした後、他のmonitor processに教えて、エラーをどう処理するかを決めてもらう。let it crashでは、以下の条件を満たす必要があります。

## エラーを処理しないではない
TODO

supervisor

one_for_one

one_for_all

rest_for_one

## 素早く再起動しなければならない

## crashしても他のところに影響を与えない

# Erlang以外の言語でlet it crashをしたら大体間違い

