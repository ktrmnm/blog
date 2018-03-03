---
title: 輸送不等式から集中不等式を出す
tags:
  - 確率論
  - 最適輸送
categories:
  - 2016/4
date: 2016-04-24 20:09:47
---

# 概要

Wasserstein距離がKLダイバージェンスで抑えられるという不等式のことを輸送不等式というのでした。例えば距離空間$(X, d)$上の確率測度$\mu$が$T\_1(C)$を満たすというのは、任意の確率測度$\nu$に対して
$$
W\_1(\mu, \nu) \leq \sqrt{C D\_{KL} (\mu, \nu)}
\tag{1}
$$
が成り立つことをいいます。

$f: X \to \mathbb{R}$がLipschitz関数のとき、
$$
\forall t > 0, \quad \Pr [ f - \mathbb{E}f \geq t] \leq \exp \left( - \frac{C t^2}{2 \lVert f \rVert\_{Lip}^2} \right)
\tag{2}
$$
が成り立つという性質を考えます。言葉でいえば「すべてのLipschitz関数の像がsub-Gaussianのように振る舞う」ということです。このような性質をGaussian concentrationと呼びます。

実は、(1)ならば(2)が成り立ちます。

## 機械学習系分野ではどう役立っているか

学習理論やランダム行列論などでは、Gaussian concentrationはとてもありがたい性質です。最終的なアプリケーションで欲しいのは、あくまで(2)の形の集中不等式なのですが、そうだとしても(1)の形で集中不等式の情報を保持していた方が何かと便利な場合があります。

例えば、学習理論では、独立同分布に従うデータについての対数尤度や経験リスクがどう振る舞うかということに興味があるわけですが、要素技術としては「単体で(2)を満たすような確率分布の$n$個の直積が再び(2)を満たすかどうか」を考える必要が出てきます。そのとき、少なくとも(1)の形の不等式は直積操作で保存されるということがわかっているので都合が良いです。集中不等式界隈（？）では、こういう性質を不等式のテンソル化 (tensorization) と呼びます。

<!--more-->
# T1不等式から集中不等式が出ること

$(X, d)$をポーランド空間とする。$\mu$を$X$上の (Borel) 確率測度とする。

**命題**

$\mu$が(1)を満たすならば、(2)も成り立つ。

**証明**

[こちら](http://ktrmnm.github.io/blog/2016/04/08/201604-transport-ineq/)に書いた、「輸送不等式の双対」を使うとすぐに出る。（双対形がそのままモーメント母関数のバウンドになっている）

$f$が$1$-Lipschitzの場合に示す。一般の場合は$\lVert f \rVert\_{Lip}$でスケールすればよい。

$\mu$が$T\_1(C)$を満たすことと、任意の有界連続関数$f$について
$$
\mathbb{E} \exp(\lambda \inf\_{y \in X} [f(y) + d(x,y)]) \leq \exp \left( \frac{\lambda^2}{2C} + \lambda \mathbb{E}f \right)
\tag{3}
$$
が成り立つことは同値である。
$f$は$1$-Lipschitzなので、
$$
\inf\_{y \in X} [f(y) + d(x,y)] = f(x)
$$
である。(3)から、
$$
\mathbb{E} \exp(\lambda [f - \mathbb{E}f]) \leq \exp(\lambda^2/2C).
$$

Chebyshevの不等式より
$$
\Pr[ f- \mathbb{E}f \geq t] = \Pr[\exp(\lambda[f - \mathbb{E}f]) \geq e^{\lambda t}]
\leq \exp(- \lambda t + \lambda^2/2C).
$$
右辺を$\lambda$について最適化すると(2)が示せる。

# 補足

上の命題は[1] Theorem 22.10の一部で、以下が成り立つことは（定数部分を除いて）すべて同値だと言っています。
- T1不等式
- T1不等式の双対
- 平均まわりのGaussian concentration
- メジアンまわりのGaussian concentration
- $\exp(a d(x\_0, x)^2)$の可積分性

そういえば、だいぶ前に集中不等式の本のゼミ用に作ったスライドがあります。輸送の話もちらっと書いているものの、今はもう少し理解しているような気がするので、時間があったらまた作り直したいなと。
<iframe src="//www.slideshare.net/slideshow/embed_code/key/9RWCJ1qykrVkGE" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/kentarominami39/1-43984243" title="集中不等式のすすめ [集中不等式本読み会#1]" target="_blank">集中不等式のすすめ [集中不等式本読み会#1]</a> </strong> from <strong><a href="//www.slideshare.net/kentarominami39" target="_blank">Kentaro Minami</a></strong> </div>

### 参考文献
[1] C. Villani. Optimal Transport: Old and New, Springer, 2009.
