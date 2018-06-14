---
layout: post
title: functional programming
---

# Table of Contents

1.  [Why](#org0195470)
2.  [関数型プログラミングとは](#orgf0ffdd0)
3.  [副作用がないコード](#orgc4c647e)
4.  [副作用がよく出る場所](#org493f589)
5.  [副作用の例１](#orga5e6b3a)
6.  [副作用がない](#orge1e5e0a)
7.  [副作用の例２](#orgc806758)
8.  [副作用はなぜよくないのか](#org04cb5b3)
9.  [状態、時間、順番全部可変の場合](#orgf6194a6)
10. [副作用を避ける](#org05019e8)
11. [状態を保持](#orgd97d94f)
12. [Redux](#org6ed51c1)
13. [関数型がよく使うパターン](#org6938c00)
14. [Stream 1](#org954f064)
15. [Stream 2](#org9bd040e)
16. [関数型のデメリット](#org5ce0cfb)
17. [オブジェクト指向との関係](#orgb10088d)
18. [まとめ](#orge29552f)


<a id="org0195470"></a>

# Why

-   最近の傾向だとオブジェクト指向と関数型が混在することが多い
    -   Underscore.js, lodash: 関数型ライブラリ
    -   Immutable.js: Immutable
    -   React: Declarative (宣言的)
    -   Redux: reduce
    -   Promise: Monad
    -   近年の新しい言語
-   学習コストが下げる
-   プロジェクトで使いたい


<a id="orgf0ffdd0"></a>

# 関数型プログラミングとは

-   できるだけ副作用がかいコードを書く
-   副作用がある場合は範囲を絞って書く


<a id="orgc4c647e"></a>

# 副作用がないコード

-   同じ条件を与えれば必ず同じ結果が得られる
-   他のいかなる機能の結果にも影響を与えない


<a id="org493f589"></a>

# 副作用がよく出る場所

-   変数
-   IO


<a id="orga5e6b3a"></a>

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


<a id="orge1e5e0a"></a>

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


<a id="orgc806758"></a>

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


<a id="org04cb5b3"></a>

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


<a id="orgf6194a6"></a>

# 状態、時間、順番全部可変の場合

プログラムの実行結果が予測できない

    const calc = new Calculator(1);
    // thread 1 で実行
    calc.plus(1);
    // thread 2 で実行
    calc.multiply(2);


<a id="org05019e8"></a>

# 副作用を避ける

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


<a id="orgd97d94f"></a>

# 状態を保持

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


<a id="org6ed51c1"></a>

# Redux

    function reducer(currentState, action) {
      pureFunc(...);
    }
    const { getState, dispatch, ... } = createStore(reducer);
    // action: {type: 'CLICK_BUTTON', value: 'v1'}
    dispatch({type: 'CLICK_BUTTON', value: 'v1'});
    dispatch({type: 'SELECT_BUTTON', value: 'v2'});


<a id="org6938c00"></a>

# 関数型がよく使うパターン

-   stream
    WebFramework の例


<a id="org954f064"></a>

# Stream 1

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
          } 
        }


<a id="org9bd040e"></a>

# Stream 2

-   データの流れ
    connection -> router() -> pipelines() -> controller() -> model()
    const connection2 = router(connection1);
    const connection3 = pipelines(connection2);
    const connection4 = controller(connection3);
    const connection5 = model(connection4);
    
    <table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">
    
    
    <colgroup>
    <col  class="org-left" />
    </colgroup>
    <tbody>
    <tr>
    <td class="org-left">&#xa0;</td>
    </tr>
    </tbody>
    </table>
    
    \_.chain(connection).router().pipelines().controller().model().value();

-   controllerの中身
    connection -> common<sub>services</sub>() -> action()
    const connection1 = common<sub>services</sub>(connection);
    const connection2 = action(connection1);


<a id="org5ce0cfb"></a>

# 関数型のデメリット

-   メモリと速度
    再帰
-   制限が多い


<a id="orgb10088d"></a>

# オブジェクト指向との関係

-   共通が多い
-   オブジェクト指向の原則は関数型も適用
    <https://blog.cleancoder.com/uncle-bob/2014/11/24/FPvsOO.html>


<a id="orge29552f"></a>

# まとめ

-   副作用を注意しましょう

