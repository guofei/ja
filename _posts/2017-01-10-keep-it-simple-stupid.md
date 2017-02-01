---
layout: post
title: keep it simple stupidのstupidについて
categories: [Programming]
---
ソフトウェアを開発するとき、KISSという原則があります。この原則では2つの解読があって、１つは「keep it simple, stupid」で、もう一つは「keep it simple and stupid」です。個人的には２つ目の方が好きです。なぜなら、２つ目のstupidは形容詞です。つまり、コードをstupidにすることになります。

# なぜコードをstupidに？
最近、４年前の自分が書いた古いコードを読む機会があって、読んでいる途中で、もっと簡単で、わかりやすい書き方があるのに、なんでそんなに「頭が良さそうな」書き方で書いたのか？という風に思う時があります。特に、印象に残っているのは、パフォーマンスの為に、ある配列のindexを3つの意味を与えることがあります。その時、一緒にコード書いた人から「頭がいいね」と言われました。しかし、今回コードを読む時、頭を使ってコードを解読する必要があります。

もし、コードを書いた時、もう少しstupidな書き方で書いたら、頭の中でコードを解読する手順が省くことができるので、もう少し楽に読めるはずです。

# コードをstupidにする例
しかし、コードをstupidにすると、他人に誤解されることがよくあります。例えば、以下に曜日を返す機能を持つ関数が２つあります。

{% highlight java %}
public String weekday1(int dayOfWeek)
{
    switch (dayOfWeek)
    {
        case 1: return "Monday";
        case 2: return "Tuesday";
        case 3: return "Wednesday";
        case 4: return "Thursday";
        case 5: return "Friday";
        case 6: return "Saturday";
        case 7: return "Sunday";
        default: throw new IllegalArgumentException("dayOfWeek must be in range 1..7");
    }
}
 
public String weekday2(int dayOfWeek)
{
    if ((dayOfWeek < 1) || (dayOfWeek > 7))
        throw new IllegalArgumentException("dayOfWeek must be in range 1..7");
 
    final String[] weekdays = {
        "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"};
 
    return weekdays[dayOfWeek-1];
}
{% endhighlight java %}

見た目ではweekday2の方が短くて、スマートな感じがします。一方で、weekday2ではif文、配列、indexの数学演算などを使っているので、少し頭を使って考える必要があります。

weekday1では非常に単純でswitchだけ使っています。少しstupidに見えるが、この関数を見る瞬間何をやっているのかがすぐわかります。

# stupidなコードを書くと誤解されることがある
stupidなコードを書くと、コードが少し入門者ぽい書き方になるので、たまに指摘されることがあります。指摘された場合、なぜこういう風に書いたのかを他人に納得させたいが、なかなか難しいです。その時、どうするかを考えたら、以下の２つの方法を思いつきました。

## コミュニケーション能力を高める
他人に納得させるために、相当なコミュニケーション能力が必要です。しかし、技術者の中でコミュニケーション能力が低い方が多いと思います。私自身も自分のコミュニケーション能力の低さに悩んで、コミュニケーション能力を少しでも高めるために、この日本語ブログを書き続くと決意しました。

## 本質を理解する
他人に納得させるために、まず自分は本質を理解する必要があります。自分は、本やネットの文章を見って、本質を理解しないまま、結論だけ覚えた時がよくあります。例えば、「c++11ではなるべくtypedefではなく、usingを使いましょう」という結論だけ覚えて、コードレビューの時、他の人に伝えたが、なぜusingを使った方が良いのかを全然覚えてないので、説明したことが全然説得力がないです。この結論は問題ないが、他人に納得させる根拠にならないので、いくら説明しても無意味になります。だから、他人に納得させたいなら、まず本質を理解することが大事です。


