---
layout: post
title: Functional Programming
categories: [Programming]
---
社内勉強会の資料です。

# Table of Contents

1.  [Why](#org0f1d58c)
2.  [関数型プログラミングの特徴](#orgc481512)
3.  [副作用とは](#orgc162155)
4.  [副作用がよく出る場所](#org3678bdb)
5.  [副作用の例１](#orgebb2cc5)
6.  [副作用がない](#orgec2195d)
7.  [副作用の例２](#org95abdc8)
8.  [副作用はなぜよくないのか](#org59eae26)
9.  [状態、時間、順番全部可変の場合](#orgfd197d2)
10. [関数型がよく使うパターン](#org85f75de)
11. [Reduceの例](#org94500bc)
12. [中間状態の取得](#orgd211a23)
13. [オブジェクト指向(カプセル化)＋関数型](#org435f0af)
14. [Redux](#org30a3c2e)
15. [Pipeline 1](#org15aa41f)
16. [Pipeline 2](#org6de5e4d)
17. [関数型のデメリット](#orge11515d)
18. [オブジェクト指向との関係](#orgd904809)
19. [まとめ](#org580c9a8)


<a id="org0f1d58c"></a>

# Why

-   最近の傾向だとオブジェクト指向と関数型の混在使用多い
    -   Underscore.js, lodash: 関数型ライブラリ
    -   Immutable.js: Immutable
    -   React: Declarative (宣言的)
    -   Redux: reduce
-   新しい言語仕様では関数型の機能が多く含まれている
    -   Java8~
        関数型インターフェース
        Lambda
    -   ES6
        Lambda
        Promise: Monad
-   チームで使いたい


<a id="orgc481512"></a>

# 関数型プログラミングの特徴

-   できるだけ副作用がかいコードを書く
-   副作用がある場合は範囲を絞って書く


<a id="orgc162155"></a>

# 副作用とは

副作用がないコード

-   同じ条件を与えれば必ず同じ結果が得られる
-   他の機能に影響を与えない


<a id="org3678bdb"></a>

# 副作用がよく出る場所

-   変数
-   IO


<a id="orgebb2cc5"></a>

# 副作用の例１

Quiz: 出力結果は？

    let v = 0;
    function inc1() {
      v++;
      console.log(v);
      return 2;
    }
    function inc2() {
      v += inc1();
      console.log(v);
      return v;
    }
    inc2();
    inc2();


<a id="orgec2195d"></a>

# 副作用がない

    function func() {
      let v = 0;
      function inc1() {
        v++;
        return 2;
      }
      function inc2() {
        v += inc1();
        return v;
      }
      return inc2();
    }
    func();
    func();


<a id="org95abdc8"></a>

# 副作用の例２

計算機

    class Calculator {
      constructor(value) {
        this.currentValue = value;
      }
      plus(value) {
        this.currentValue += value;
      }
      multiply(value) {
        this.currentValue *= value;
      }
    }
    const calc = new Calculator(1);
    calc.plus(1);
    calc.multiply(2);


<a id="org59eae26"></a>

# 副作用はなぜよくないのか

-   状態が可変
    -   毎回実行結果が変わるかどうか
    -   内部状態が変わったかどうか
    -   どこで変わったのか
-   順番と時間を意識する必要がある
    
        // 1
        calc.plus(1);
        calc.multiply(2);
        // 2
        calc.multiply(2);
        calc.plus(1);


<a id="orgfd197d2"></a>

# 状態、時間、順番全部可変の場合

プログラムの実行結果が予測できない

    const calc = new Calculator(1);
    // thread 1 で実行
    calc.plus(1);
    // thread 2 で実行
    calc.multiply(2);


<a id="org85f75de"></a>

# 関数型がよく使うパターン

-   map
-   reduce
-   filter
-   pipeline(stream)

メリット

-   副作用を避けられる
-   ループを抽象化
-   関数組み合わせで再利用可能


<a id="org94500bc"></a>

# Reduceの例

    function reducer(accumulator, {type: t, value: v}) {
      switch (t) {
        case '+':
          return plus(accumulator, v);
        case '*':
          return multiply(accumulator, v);
        default:
          return accumulator;
      }
    }
    const input = [{type: '+', value: 1}, {type: '*', value: 2}];
    _.reduce(input, reducer);


<a id="orgd211a23"></a>

# 中間状態の取得

    const state0 = {};
    const state1 = reducer(state0, {type: '+', value: 1});
    const state2 = reducer(state1, {type: '*', value: 1});


<a id="org435f0af"></a>

# オブジェクト指向(カプセル化)＋関数型

    const createStore = (reducer) => {
      let state;
      const getState = () => state;
      const dispatch = (action) => {
        state = reducer(state, action);
      };
      dispatch({});
      return { getState, dispatch };
    };
    const { getState, dispatch } = createStore(reducer);
    dispatch({type: '+', value: 1});
    // stateの中身は変更不可（read only）
    const state = getState();
    dispatch({type: '*', value: 2});


<a id="org30a3c2e"></a>

# Redux

    function reducer(currentState, action) {
      pureFunc(...);
    }
    const { getState, dispatch, ... } = createStore(reducer);
    // action: {type: 'CLICK_BUTTON', value: 'v1'}
    dispatch({type: 'CLICK_BUTTON', value: 'v1'});
    dispatch({type: 'SELECT_BUTTON', value: 'v2'});


<a id="org15aa41f"></a>

# Pipeline 1

Web Framework

-   データ構造
    
        const connection = {
          request: {
            host: 'www.abc.com',
            request_path: '/',
            req_headers: {},
          },
          response: {
            status: 200,
          },
          assigns: {
            userData1: 'user',
          },
          ...
        }


<a id="org6de5e4d"></a>

# Pipeline 2

Web Framework

-   データの流れ
    connection -> router() -> pipelines() -> controller() -> model()
    const connection2 = router(connection1);
    const connection3 = pipelines(connection2);
    const connection4 = controller(connection3);
    const connection5 = model(connection4);

-   controllerの中身
    connection -> common<sub>services</sub>() -> action()
    const connection1 = common<sub>services</sub>(connection);
    const connection2 = action(connection1);


<a id="orge11515d"></a>

# 関数型のデメリット

-   メモリと速度
    JavaScript再帰
-   制限が多い


<a id="orgd904809"></a>

# オブジェクト指向との関係

-   共通が多い
-   オブジェクト指向の原則は関数型も適用
    <https://blog.cleancoder.com/uncle-bob/2014/11/24/FPvsOO.html>


<a id="org580c9a8"></a>

# まとめ

-   副作用を注意しましょう



