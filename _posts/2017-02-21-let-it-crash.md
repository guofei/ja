---
layout: post
title: Let it crashについて
categories: [Programming]
---
Elixir/Erlangを勉強してなかった時、c++の例外処理について、例外の安全性の確保が難しいことと例外処理を入れるとコードが理解しにくくなることを認識して、もっと良い手段がないかを調べたら、let it crashという言葉を知り、誤解のままc++のプロジェクト（デスクトップアプリ）で使ってしまいました。例えば、エラー処理するべきところに変なassertを入れて、ユーザーから予想外の操作がされたら思い切り落ちってしまいます。そのあとassertするコードを排除したが、let it crashの概念がずっと誤解したままでした。ElixirでOTPを勉強したら予約let it crashについて少し理解できたので、メモを残ります。

# let it crashとは何か？
Erlangでは、不明なエラーが起こったとき、防御的なプログラミングをすることではなく、processをcrashさせる。crashした後、他のmonitor processに教えて、エラーをどう処理するかを決めてもらう。let it crashでは、以下の条件を満たす必要があります。

## エラーを処理しないではない
let it crashはエラー処理しないと思ってたが、大きな間違いです。エラー処理しないではなく、プログラマーの代わりにエラー処理を手伝ってくれる「人（process）」がいるのです。

手伝ってもらう前に、crashが起こったことをお知らせする必要があります。erlangでは、linkやmonitorを用いって、crashした情報を外部に通知します。さらに、OTP frameworkでは、この仕組みをもっと抽象化して、supervisorという専用のprocessを提供します。このsupervisor processは他のprocessの管理の仕事がされてます。

supervisorは以下のようなストラテジに従って、processを管理します。

* one_for_one
ある子プロセスが終了したら、そのプロセスだけを再起動します。

* one_for_all
ある子プロセスが終了したら、他のすべての子プロセスを再起動します。

* rest_for_one
ある子プロセスが終了したら、そのプロセスの次のすべての子プロセスを再起動します。

詳しい説明はこちらへ：http://erlang.org/doc/design_principles/sup_princ.html

## Processの起動・再起動のoverheadを極小にしなければならない
極端な例で考えると、あるprocessが１００万のユーザーとつながっています。このprocessを再起動するのに１秒もかかります。この場合、processを再起動すると、１秒間の間で、１００万のユーザーがサービスを利用不可になってしまいます。

Erlangの場合、processの起動のoverheadがすごく小さいので、１ユーザーに対して、１processを起動することがほとんどです。

## crashしても他のところに影響を与えない
multi threadの場合、メモリが共有さてたので、一つのthreadがcrashしたら、他のthreadもおかしくなる時がよくあります。この場合、勝手にlet it crashしたら、すべてのデータが壊れてしまいます。

Erlangの場合、actor model＋immutable dataのおかげで、crashしてもエラーが隔離されて、他のところに影響を与えなません。

# Erlang VM系以外の言語でlet it crashをしたら大体間違い
他の言語では、上のような仕組みをない（akkaを除く）ので、let it crashしたら、ほぼ間違いじゃないでしょう。


