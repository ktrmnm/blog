---
title: MathJax数式についてのメモ
date: 2016-03-22 03:11:22
categories:
- 2016/3
tags:
- 数学
- MathJax
---

# はじめに

[Hexo](https://hexo.io)でブログを生成した。理由はLaTeX数式を労力なく書けそうだからである。
とはいえ、MathJaxでものを書くのは初めてだ。ここではHexoプラグインの導入方法と、MathJaxの記述方法についてまとめておく。

## プラグイン導入

[hexo-math](https://github.com/akfish/hexo-math)プラグインを利用することにした。
```bash
$ npm install hexo-math --save
```
でインストールし、Hexoプロジェクトの設定ファイル `_config.yml` に以下を記述する:
```
math:
  engine: 'mathjax'
  mathjax:
    src: http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML
    config:
      tex2jax:
        inlineMath: [ ['$','$'], ["\\(","\\)"] ]
        displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
```
（2016/3/22現在）

## 基本的な記述

行内数式はLaTeXと同様で `$ f(x) = x^2 $`のように書けばよい: $f(x)=x^2$.
別行立てで書くには `$$` で挟んで書く。
```
$$
\mathbb{E} \max_{s,t\in S: d(s,t) \leq \delta} |X(t)-X(s)| \leq (16 \sqrt{2} + 2)
\int_0^\delta \sqrt{\log 2N(T, d, \varepsilon)} \mathrm{d}\varepsilon
\tag{999}
$$
```
$$
\mathbb{E} \max_{s,t\in S: d(s,t) \leq \delta} |X(t)-X(s)| \leq (16 \sqrt{2} + 2)
\int_0^\delta \sqrt{\log 2N(T, d, \varepsilon)} \mathrm{d}\varepsilon
\tag{999}
$$

なるほどなあ。むしろ、何ができないのか試したくて`\mathbb{E}`などを試したのだが、普通に使えるようなのでありがたい。これで$\mathbb{R}$とか$\mathbb{Z}$なども使い放題である。その他に使えるLaTeXコマンドの一覧は[MathJaxのドキュメント](http://mathjax.readthedocs.org/en/latest/tex.html)に載っている。

## マクロ
MathJaxではマクロも定義できるが、今回は必要に応じて次のように書けばよい。YAMLなせいか心持ちすっきりと書けている気がする。
{% codeblock _config.yml %}
math:
  engine: 'mathjax' # or 'katex'
  mathjax:
    src: http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML
    config:
      tex2jax:
        inlineMath: [ ['$','$'], ["\\(","\\)"] ]
        displayMath: [ ['$$','$$'], ["\\[","\\]"] ]
      TeX:
        Macros:
          dd: \mathrm{d}
          RR: \mathbb{R}
          argmax: \operatorname*{argmax}
{% endcodeblock %}
例えば上では`\argmax`を定義したので次のように書ける：
```
$$
\argmax_{t \in T} f(t)
$$
```
$$
\argmax_{t \in T} f(t)
$$

## Hexo特有の記法
ところで、Hexoにはこれとは別に[Hexo記法](https://hexo.io/docs/tag-plugins.html)というものが存在し、次のように書くことで同様の結果が得られる。
```
{% math %}
\mathbb{E} \max_{s,t\in S: d(s,t) \leq \delta} |X(t)-X(s)| \leq (16 \sqrt{2} + 2)
\int_0^\delta \sqrt{\log 2N(T, d, \varepsilon)} \mathrm{d}\varepsilon
{% endmath %}
```
{% math %}
\mathbb{E} \max_{s,t\in S: d(s,t) \leq \delta} |X(t)-X(s)| \leq (16 \sqrt{2} + 2)
\int_0^\delta \sqrt{\log 2N(T, d, \varepsilon)} \mathrm{d}\varepsilon
{% endmath %}
ただし、hexo-mathの現在の仕様では、mathブロックの中に単一の行しかないときはインライン数式、それ以外はディスプレイ数式として勝手に解釈される。個人的にはちょっと不便だなとも思う。