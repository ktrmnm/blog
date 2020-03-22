---
title: (勉強メモ) Information bottleneck (1)
tags:
  - 機械学習
  - 情報理論
  - 個人用メモ
categories:
  - 2020/3
date: 2020-03-22 15:59:07
---

Information bottleneck関係の個人的勉強メモです。

ブログでは、書くことの心理障壁を下げるために以前よりも勉強内容を小出しにしていくことを考えており、今日の目標は以下の元論文の主張をまとめておくことです。

- Tishby, Pereira, and Bialek (2000). The information bottleneck method. [(URL)](https://arxiv.org/abs/physics/0004057)

# 背景

Information bottleneck法 (Tishby et al. 2000) とは、[レート歪み理論](https://en.wikipedia.org/wiki/Rate%E2%80%93distortion_theory) を一般化した概念である。レート歪み理論は非可逆圧縮の達成限界についての理論で、符号の空間のサイズ (レート) と復号時のエラー (歪み) の間のトレードオフを記述したものである。具体的に、このトレードオフは、「レート歪み関数」という、相互情報量をともなう最適化問題の最適値によって書ける。レート歪み関数の定義には、復号時のエラーを定義するための損失関数 (歪み関数) を指定する必要があるが、実用上は適切な損失関数を選ぶことが難しいことがある。Information bottleneck法では、$X$ に対する損失関数は陽に指定せず、その代わりに、符号のサイズは小さくしつつ別の信号 $Y$ に関係する情報を最大限保つという目的関数を考えることで、「$X$ の中の $Y$ に関係する情報」を抽出する問題を定式化する。例えば、$X$ を入力として $Y$ を予測する機械学習の問題があるとき、$Y$ の予測のために必要な情報はなるべく失わずに、最小限の大きさの特徴量 $\hat{X}$ を抽出したいというような状況が考えられるが、それを情報理論 (レート歪み理論) 的に解釈して定式化したのがinformation bottleneckであるというイメージ。

レート歪み理論の歪み (distortion) は「ひずみ」と読みます。

# Information bottleneckの基礎

## レート歪み理論

確率変数 $X$ を有限集合に値をとる符号に圧縮したい。$X$ の分布 $p(x)$ は既知とする。エンコーダーとデコーダーは確率的でもよいとし、まず (i) エンコーダーによって $X$ を $Z$ に符号化し、次に (ii) デコーダーによって $Z$ を $\hat{X}$ に復号化するプロセスを、まとめて条件付き分布 $p(\hat{X} \mid X)$ で表現するものとする。このプロセスの良さをはかるために、損失関数 $d$ を使って

$$
    D = \mathbb{E} [d(X, \hat{X})]
$$

によって損失を定義する。

$Z$ が値をとる空間のcardinalityの対数を $R$ と書き、レートと呼ぶ。原理的には、$R$ を増やすと信号 $X$ を無限に細かく離散化できるので、歪み $D$ は小さくなるはずである。逆に、$R$ を減らすと、表現力の低い符号によって $X$ の挙動を説明しなければならなくなるので、$D$ は大きくなる。したがって $R$ と $D$ にはトレードオフがあるが、このトレードオフの意味で最適なフロンティアがどこにあるか知りたい。$D > 0$ を与えたときに、可能な限り最も短い $R$ を $R(D)$ と書く (これをレート歪み関数という)。

レート歪み理論によると、$R(D)$ は次の最適化問題の最適値に等しい。

$$
    \min_{p(\hat{x} \mid x)} I(X, \hat{X})
    \text{ s.t. } \mathbb{E} [d(X, \hat{X})] \leq D
    \tag{1}
$$

つまり、歪みが $D$ 以下に抑えられるようなエンコーダー/デコーダー対 $p(\hat{x} \mid x)$ のなかで、$X$ と $\hat{X}$ の相互情報量の最小値が、ぎりぎり達成可能な符号のサイズに対応している。

たまたま最近Cover and Thomasの輪読会をやったので、証明はその本で読んだ。$R \geq I(X, \hat{X})$ のようなことは不等式評価ですぐにわかるのだけど、達成可能性のほうは具体的なエンコーダー/デコーダーを作らなければならず、ちょっと込み入っているように感じた。他の方法はないのかと思って2冊目も確認したけど、だいたい同じ証明だった。

- Cover and Thomas. [Elements of Information Theory](https://www.amazon.co.jp/dp/0471241954/ref=cm_sw_r_tw_dp_U_x_2hZDEb19PR3PV) のChapter 10
- Han. [Information-Spectrum Methods in Information Theory](https://www.amazon.co.jp/Information-Spectrum-Information-Stochastic-Modelling-Probability/dp/3642078125) のTheorem 5.2.1

## レート歪み関数の計算

現実には (1) の最適化問題の解や最適値をclosed formで計算するのは難しいので、数値計算のアルゴリズムが提案されている。ここでは、[Tishby et al. (2000)](https://arxiv.org/abs/physics/0004057) で紹介されているBlahut--Arimoto (BA) アルゴリズムについて書く。

(1) のLagrangianを書くと
$$
    I(X, \hat{X}) + \beta (\mathbb{E} [d(X, \hat{X})] - D)
    \tag{2}
$$
となる。$\beta$ はLagrange乗数である。

ここで、($D$ そのものではなく) $\beta > 0$ を与えたときにLagrangianを最小化する問題を考えてみる。Lagrangianの $p(\hat{x} \mid x)$ に関する変分を考えると、停留条件は

$$
    p(\hat{x} \mid x) \propto p(\hat{x}) \exp( - \beta d(\hat{x}, x))
$$

のようになる。この停留条件の式に $p_t(\hat{x} \mid x)$ を再代入していくアルゴリズムを考えると、やがて不動点に収束することが示される。これがBAアルゴリズムと呼ばれている。

BAアルゴリズムは通信路容量を計算するのにも使われる。探したら以下のスライドがあった。収束性についてちゃんと勉強していないので、そのうち勉強したい。

- [スライド](http://www.inc.cuhk.edu.hk/InformationTheory/files/Abridged/Ch_9.pdf) (by Raymond W. Yeung)

## Information bottleneck method

現実には歪み関数 $d$ として適切なものが何であるのか判断するのが難しい状況がある。代わりに、「ある別の変数 $Y$ に関する情報を最大限保ちつつなるべく小さな空間にエンコードする」という問題を考えるというのがinformation bottleneckの発想である。具体的には、(2) の代わりに

$$
    I(X, \hat{X}) - \beta I(\hat{X}, Y)
    \tag{3}
$$

を最小化する $p(\hat{x} \mid x)$ を考える。それぞれの項について考えてみると、

- $I(X, \hat{X})$ はもとのレート歪み理論の定式化 (2) にも出てくる項で、これが小さければ小さいほど圧縮率が高い符号を意味する。例えば、$X$ を1点集合に潰してしまうのが自明に最も短い符号だけど、このとき $I(X, \hat{X}) = 0$ となる。逆に、$X$ を無限に細かくquantizeすれば無限に高精度で復号できるが、上限値として $I(X, \hat{X}) = I(X, X) = H(X)$ にどんどん近づいていくはず。
- $- \beta I(\hat{X}, Y)$ は符号化によって $Y$ の情報をなるべく失わないための正則化である。データ処理不等式から一般に $I(X, Y) \geq I(\hat{X}, Y)$ となるから、エンコード/デコードの処理で $Y$ に関する情報が増えることはない。

よって、第1項を最小化することはなるべく小さく圧縮することを目指すのに対して、第2項を最小化することはなるべく元の情報を保つことを目指すので、どこかに均衡点が発生する。

Information bottleneckの式 (3) の停留条件を書くと、

$$
    p(\hat{x} \mid x) \propto p(\hat{x}) \exp( - \beta D_{KL}(p(y \mid x) \parallel p(y \mid \hat{x})))
$$

となる。よって、information bottleneck法では、結果的に $D_{KL}(p(y \mid x) \parallel p(y \mid \hat{x}))$ を歪み関数として採用したことになっているといえる。

[元論文](https://arxiv.org/abs/physics/0004057) では、(3) を解くためのBlahut--Arimoto型のアルゴリズムも与えられている。

# 次回メモ

もともと、最近の機械学習 or 深層学習の文脈で、information bottleneckが (思想として) どう理解されているのか気になったので、次回はより最近の論文をランダムに読みます。特に、深層学習の汎化の説明として、Tishby and Zaslavsky (2015) がinformation planeという概念を導入しており、そのあたりの整理が目標です。

- Tishby and Zaslavsky (2015). Deep Learning and the Information Bottleneck Principle. [(URL)](https://arxiv.org/abs/1503.02406)
- Shwartz-Ziv and Tishby (2017). Opening the Black Box of Deep Neural Networks via Information. [(URL)](https://arxiv.org/abs/1703.00810)
- Saxe et al. (2018). On the Information Bottleneck Theory of Deep Learning. [(URL)](https://openreview.net/forum?id=ry_WPG-A-)
- Alemi et al. (2016). Deep Variational Information Bottleneck. [(URL)](https://arxiv.org/abs/1612.00410)
- [yoheikikutaさんのメモ (The information bottleneck method)](https://github.com/yoheikikuta/paper-reading/issues/28)
- [yoheikikutaさんのメモ (Deep Learning and the Information Bottleneck Principle)](https://github.com/yoheikikuta/paper-reading/issues/26)

