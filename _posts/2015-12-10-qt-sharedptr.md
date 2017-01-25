---
layout: post
title: c++のshared_ptrとQtのgui部品を同時に使う時の注意
date: 2015-12-10 00:23:58 +0900
categories: [c++]
---
Qtのguiの部品は独自のメモリ管理機能があるので、std::shared_ptrの混合して使う場合は注意しないといけない。

{% highlight cpp %}
    QApplication app(argc, argv);

    auto button1 = std::shared_ptr<QPushButton>(new QPushButton("Hello"));
    auto button2 = std::shared_ptr<QPushButton>(new QPushButton("World"));

    QWidget widget;
    QHBoxLayout layout(&widget);

    layout.addWidget(button2.get());
    layout.addWidget(button1.get());
    widget.show();
    return app.exec();
{% endhighlight cpp %}

上のbutton1とbutton2はstd::shared_ptrで作ったので、shared_ptrが一回解放してくれる。しかし、button1,button2がwidgetに追加されるので、widgetがなくなる前に、子供を解放されるので、button1,button2が２回解放される。

そのため、Qtのgui部品を作る時、shared_ptrをなくべく使わないほうがいいかな？

