---
layout: post
title: Phoenix 1.3：boundaryを意識しての開発
categories: [Phoenix, Programming]
---
Phoenix 1.3ではBoundaryという概念がすごく強調されました。その結果として、ディレクトリの構成が変わりました。

PhoenixはもともとRailsを真似して作ったので、model，controller，view，templateのようなディレクトリ構成になっていました。しかし、この構成だと、
詳しくはこちらへ[Screaming Architecture
](https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html)

