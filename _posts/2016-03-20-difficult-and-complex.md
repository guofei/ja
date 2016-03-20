---
layout: post
title: プログラミングの難しさと複雑さ
---
## 難しくて、複雑ではないのも

例えば、難しい数学公式があって、公式を理解するのが難しいが、公式のままにコードを書けば、割と簡単でで綺麗に書ける。なぜなら、数学の公式はすでに**美しく抽象化**さている。

しかし、実際では、美しく抽象化さてた数学公式を色々分解したり、ラッパしたり、継承したりして、抽象化が破壊さてれ、コードが複雑になるケースがよくある。あるいは、数学の公式をそのまま書くと、パフォーマンスが\\
よくないので、色々高速化を行う時、公式の抽象化が破壊されていまう場合もよくある。

## 簡単で、複雑なもの

UIを作るとき、ボタンを追加するには難しくないはずだが、すごく複雑な場合がある。例えば、あるボタンをクリックし、複数の挙動が行われる場合、正しくコードを書ける人が少ないし、その部分まだ機能を追加するのも難\\
しくなる。なぜなら、状態が多すぎて、**いつ変わったのか、どこで変わったのか、どういう理由**で変わったのかが複雑すぎて、うまくコントロールできない。

こういう時、ちゃんと設計したり、パタンを使ったりして、状態をうまく管理して、UI部品を抽象化する必要がある。

## 難しくて、複雑なもの

マルチスレッド