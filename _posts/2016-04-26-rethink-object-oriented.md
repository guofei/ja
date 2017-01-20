---
layout: post
title: オブジェクト指向の再考
---
仕事で長くC++を使いました．書いたプログラムを綺麗するために，C++に関する本をいろいろ読んだり，コードをリファクタリングしたりしたが，やはりC++は複雑しすぎて，コードの量が膨大になるとコントロールできない感じが出ます．特に，コードの中に継承やテンプレートなどが入ると後からすごく読みにくになります．そこで，最近オブジェクト指向を再考し，オブジェクト（classというキーワード）の使用を控えるようにしました．

## なぜオブジェクトの使用をを控える
### データ中心のものだとロジックと混ぜると理解しにくくなる
今の仕事はCFDソフトを開発しているので，ソフトの中で主に扱うものは三次元の点，線，面，立体などの集まりです．そういう点や面を単純にデータとして考えるのは非常にシンプルになります．しかし，ロジックが混ぜると結構ややこしくなります．人間はロジックよりデータの方がはるかに理解しやすいからです（[The Art of Unix Programming: Fold knowledge into data, so program logic can be stupid and robust.](http://www.faqs.org/docs/artu/ch01s06.html#id2878263)）．

### オブジェクトの設計が難しい
きちんと設計しないとGod Classになったり，Classの責任が不明になったりします．逆にきちんと設計した場合，過度に仕組みを作ってしまう傾向もあります．

### オブジェクトにしべき無いものがある
有名なのは”時間”という例です．[Why OO Sucks by Joe Armstrong: Objection 2 - Everything has to be an object](http://harmful.cat-v.org/software/OO_programming/why_oo_sucks)

## 使い分け

### いつオブジェクトを使う
データがよく変わるが，振る舞いがあまり変わらないもの．例えば，UIのコンポーネントを作るとき，クリック，ドラッグなどのような操作は決まっているものが多いので，オブジェクトで作ったほうがいいです．

### いつオブジェクトを使わない
データがそんなに変わらないが，振る舞いがよく変わるもの．例えば，今の仕事で使っている点，線，面などのデータ構造では，データとして全然変わらないので，オブジェクトを使う意味はないと思います．

自然に純粋な関数になるものをオブジェクトとして使わない。例えば、 x^2 (xはint)を計算する場合、
{% highlight cpp %}
int square(int x) {
  return x * x;
}
{% endhighlight cpp %}
をすればいいです。わざわざ以下のように書くと、余計な状態が出てきて、プログラムが複雑になるし、bugも起こりやすくなってしまいます。
{% highlight cpp %}
class Square
{
public:
  Square(int x) {
    value = x;
  }
  int calc() {
    return value * value;
  }
private:
  int value = 0;
}
{% endhighlight cpp %}

