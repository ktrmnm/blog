---
title: 輸送不等式の双対
categories:
- 2016/4
tags:
  - 確率論
  - 最適輸送
date: 2016-04-08 00:00:00
---

# ひとことまとめ

輸送不等式の双対表現がおもしろかったので。

# 輸送不等式

最適輸送 (optimal transport) というジャンルがある。元々どういう問題なのかは[1]などに譲るとするが、最適輸送で出てくる色々なツールが、他の問題を考えるときにも色々と有益だということが知られていて、確率論の別のジャンルでもしばしば使われる。

$(X, d)$を距離空間とする。$\mu, \nu$を$X$上の (Borel) 確率測度とする。$X^2$上の確率測度$\pi$で、軸方向への周辺分布がそれぞれ$\mu$と$\nu$に等しいようなものを**カップリング**という。

カップリングは、「輸送計画」みたいなものに相当する。つまり、$X$という空間上にある$\mu$という山を切り崩して$\nu$という山に移し替えたい。どの部分を切り取ってどこに持っていくかということを表したものが$\pi$である。
ところが、山を切り崩して移動するというのをタダで行えるわけではない。$X$上の点$x\_1$から$x\_2$に荷物を運ぶのに、$x\_1$と$x\_2$の距離が遠いほど多くコストがかかるかかるという状況を考える。沖縄などの地域で通販の送料が無料にならなかったりするのはそういう理由だろう（沖縄に住んでいらっしゃる方はすみません）。例えば、単位体積を$x\_1$から$x\_2$に運ぶのに、$d^p(x\_1, x\_2)$円かかるとする。このとき、全体での輸送コストは
$$
\int\_{X^2} d^p(x\_1, x\_2) \dd \pi(x\_1, x\_2)
$$
のと書ける。このコストを最小化するように輸送計画を設計しようというのが、最適輸送という問題である。

上の最小値を
$$
\inf\_\pi \int\_{X^2} d^p(x\_1, x\_2) \dd \pi(x\_1, x\_2)
$$
とする。実は、輸送するためのコストが最小限いくらかかるかで、確率測度のあいだの距離が測れる。実際、
$$
W\_p(\mu, \nu) := \left( \inf\_\pi \int\_{X^2} d^p(x\_1, x\_2) \dd \pi(x\_1, x\_2) \right)^{1/p}
$$
は距離になるが、これを[Wasserstein距離](https://en.wikipedia.org/wiki/Wasserstein_metric)という（注1）。

$\mu$と$\nu$のKLダイバージェンスを$D(\mu, \nu)$と書く（定義は下）。この記事でいう輸送不等式というのは、次のようなものである：
任意の確率測度$\nu$に対して、不等式
$$
W\_p^p(\mu, \nu) \leq \sqrt{C D(\mu, \nu)}
\tag{1}
$$
が成り立つとき、$\mu$は輸送不等式$T\_p(C)$をみたすという。

<!-- more -->

[Pinskerの不等式](https://en.wikipedia.org/wiki/Pinsker%27s_inequality)は輸送不等式の例になっている。というのも実は、[全変動距離](https://en.wikipedia.org/wiki/Total_variation_distance_of_probability_measures)はハミング距離$d(x,y) = 1\_\{ x \neq y \}$に対するWasserstein距離とみなせる。
$$
\lVert \mu - \nu \rVert\_\mathrm{TV} = \sup |\mu(A) - \nu(A)| = \inf\_\pi \int 1\_\{ x \neq y\} \dd \pi(x,y)
$$
だから上の表記でいえばPinskerの不等式
$$
\lVert \mu - \nu \rVert\_\mathrm{TV} \leq \sqrt{\frac{1}{2} D(\mu, \nu)}
$$
は$T\_1(1/2)$ということになる。

$T\_2(C)$にも有名な例がある。
$\mathbb{R}^n$に通常のEuclid距離を入れた空間を考える。$n$次元標準正規分布$\gamma_n$について、Talagrandの輸送不等式
$$
W_2^2(\nu, \gamma_n) \leq \sqrt{2 D(\nu, \gamma_n)}
$$
が成り立つことが知られている。上の表記でいうと、$\gamma_n$は$T\_2(2)$をみたすということになる。

ところで、$T\_p(C)$には双対表現というべき等価な不等式があって、それを導出するのに色々な双対が出てきておもしろい。

# いろんな双対

最適輸送コストとKLダイバージェンスには、それぞれの文脈から出てきた双対公式がある。
世の中にはいろんな双対があるなあ（詠嘆）。

## Kantrovich双対性
$C\_b(X)$を$X$上の有界連続関数の全体とする。
最適輸送コストについて、次のような等式が成り立つ。
$$
\begin{align}
W^p\_p(\mu, \nu) & = \inf\_\pi \int\_{X^2} d^p(x, y) \dd \pi(x, y) \\\\
& = \sup \left \\{
\int f(x) \dd \mu(x) + \int g(y) \dd \nu(y) :\; f, g \in C\_b(X), \; f(x) + g(y) \leq d^p(x, y)
\right \\}
\end{align} 
\tag{2}
$$ 
これをKantrovich双対性という。最適化がわかる人向けにいうと、最適輸送問題の双対問題は
$$
\begin{align}
\max \quad & \int f(x) \dd \mu(x) + \int g(y) \dd \nu(y) \\\\
s.t. \quad & f(x) + g(y) \leq d^p(x, y)
\end{align}
$$
みたいなものに相当するらしく、しかも相補性が成り立つらしい。

## KLダイバージェンス
$\mu$と$\nu$のKLダイバージェンスを
$$
D(\mu, \nu) = \int \log \frac{\dd \mu}{\dd \nu} \dd \mu
$$
と定義する。ただし、$\mu \ll \nu$でないときは$+\infty$とする。KLダイバージェンスには次のような双対公式がある。
$$
\log \int \exp(f(x)) \dd \nu(x) = \sup\_{\mu: \; \mu \ll \nu} \left( \int f(x) \dd \mu  - D(\mu, \nu) \right)
\tag{3}
$$
Donsker-Varadhanの変分公式ともいうらしい。ちなみに[変分ベイズ](https://en.wikipedia.org/wiki/Variational_Bayesian_methods)の原理とかはこれにとても近い。KLを最小化したければ双対表現を最小化すればいいじゃない、という発想。

また、今回は使わないが、
$$
D(\mu, \nu) = \sup\_{f \in C\_b(X)} \left \\{
\int f(x) \dd\mu(x) - \log \int \exp(f(x)) \dd \nu(x)
\right \\}
\tag{4}
$$
という双対公式も存在する[3]。

## ルジャンドル変換
道行く人に「あなたの好きな双対は何ですか」と聞いてまわったらFenchel双対はベスト5くらいに入るんじゃないか。

それはさておき、下でちょっと使うので、つまらない例だけれど復習しておく。
$x \geq 0$で定義された凸関数$h(x) = a x^2$ ($a > 0$) の[ルジャンドル変換](https://ja.wikipedia.org/wiki/ルジャンドル変換)は

$$
h^{\*}(y) = \max\_{y \geq 0} (xy - h(x)) = \frac{y^2}{4a}
$$
である。
凸関数のルジャンドル変換のルジャンドル変換は元に戻る。
$$
h^{\*\*}(x) = \max\_{x \geq 0} (yx - h^\*(y)) = ax^2 = h(x)
$$


# 輸送不等式の双対

Kantrovich双対とKLダイバージェンスの双対とを合わせることで、輸送不等式の双対形が得られる。
もともと$T\_p(C)$というのは「すべての確率測度についてhoge」という主張だったのだが、双対形のほうは「すべての有界連続関数についてpiyo」という主張になる。

**定理** 
$\mu$が$T\_p(C)$を満たすことは次のことと同値である：
任意の$f, g \in C\_b(X)$ s.t. $f(x) + g(y) \leq d^p(x, y)$に対して、
$$
\forall t \geq 0, \quad \int \exp(tf(x)) \dd \mu(x)
\leq \exp \left( - t \int g(x)\dd \mu + \frac{Ct^2}{4} \right).
$$

**証明**
Kantrovich双対性(2)より、$T\_p(C)$であることと、任意の$f, g \in C\_b(X)$ s.t. $f(x) + g(y) \leq d^p(x, y)$および任意の$\nu$に対して、
$$
C^{-1}\left( \int f(x) \dd \nu(x) + \int g(y) \dd \mu(y) \right)^2 \leq D(\nu, \mu)
\tag{5}
$$
が成り立つことと同値である。
$h(x) = C^{-1}x^2$のルジャンドル変換を考えることにより、$h(x) = \sup\_t (tx - Ct^2/4)$であるから、(5)は
$$
\forall t \geq 0, \int tf(x) \dd \nu(x) - D(\nu, \mu) \leq -\int tg(x) \dd \mu(x) + \frac{Ct^2}{4}
$$
と同値である。
Donsker-Varadhanの公式(4)より、左辺を$\nu$について最適化すると、
$$
\log \int \exp(tf(x))\dd \mu(x) \leq -\int tg(x) \dd \mu(x) + \frac{Ct^2}{4}
$$
であるから示された。

証明は[3]を参考にした。$p = 1, 2$の場合は[2]が初出とのこと。

### 参考文献

[1] Villani. Optimal Transport: Old and new, Springer, 2009.
[2] Bobkov and Götze. Exponential integrability and transportation cost related to logarithmic Sobolev inequalities, J. Fucnt. Anal., 163(1):1--28, 1999.
[3] Gozlan and Leonard. Transport inequalities. A survey, Markov Processes and Related Fields, 16(4):635--736, 2010.

(注1) Wasserstein距離は、他の距離的な尺度（total variation, KLダイバージェンス, etc.）と違って、$X$上の距離$d$の情報が確率測度間の距離に陽に反映されているということで、最近は機械学習の学会でもちらほら応用を見かける。