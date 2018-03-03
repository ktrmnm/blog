---
title: Generic Chaining (3)
tags:
  - 確率論
  - 学習理論
  - 経験過程論
categories:
  - 2016/4
date: 2016-04-12 20:22:56
---
# 前提
Generic chainingのタイトさを個人的に理解するためのノートの3つめ。
- [その1](http://ktrmnm.github.io/blog/2016/03/23/20160322-generic-chaining-1/)
- [その2](http://ktrmnm.github.io/blog/2016/03/25/20160325-generic-chaining-2/)

実は、とてもわかりやすい資料[1]を発見したため、あまり自分でリライトする意味がなくなりつつある。でも一応自分の言葉でまとめておく。

$X\_t$は平均0の$d$-sub-Gaussian processとする。つまり、増分がsub-Gaussianであるような確率過程とする：
$$
\Pr(| X\_s - X\_t| \geq u) \leq 2 \exp \left( - \frac{u^2}{2d(s,t)^2}\right).
\tag{1}
$$
下つきの添字がスペース上の都合で書きにくいときは$X\_t = X(t)$とも書く。

特に、$X\_t$がガウス過程のときは、距離$d$として、$X\_t$から定まる内在的な距離
$$
d(s, t) = \sqrt{\mathbb{E}(X\_t - X\_t)^2}
$$
を用いればよい。

平均0なので、$t\_0 \in T$を固定すれば
$$
\mathbb{E}\sup\_t X(t) = \mathbb{E}\sup\_t (X(t) - X(t\_0))
$$
が成り立つ。左辺に興味がある場合も、右辺を考えた方が今は都合がよい。$Y = \sup\_t (X(t) - X(t\_0))$は非負なので、
$$
\mathbb{E} Y = \int\_0^\infty \Pr(Y \geq u) \dd u
$$
が成り立つ。

<!--more-->

# Generic chainingによる上界
連鎖 (chaining) というのは次のような考え方であった。$T$の代表点の集合$T\_n$の増加列をつくり、$T\_n$の要素数が増大するペースと、近時の第$n$階層と$(n+1)$階層の間の分散が減るペースをコントロールする。分散と要素数がコントロールされたsub-Gaussian変数の最大値（の期待値）をバウンドする不等式を用いて、$|X(\pi\_n(t)) - X(\pi\_{n+1}(t))|$の最大値をまとめ上げることによって、目的の$|X(t)-X(t\_0)|$の最大値をバウンドする（[前回](http://ktrmnm.github.io/blog/2016/03/25/20160325-generic-chaining-2/)参照）。

上の操作をもっとシステマティックに整理したのがgeneric chainingによる上界と言える。
改善の余地があるとしたら、直感的には次の二点な気がする：
- もともとのchainingでは、$T\_n$は$T$の$2^{-n}$-netになるようにとった。つまり、$T\_n$の点はいわば等間隔に選んでいたが、冷静に考えると必ずしもそうである必要はない。$T$という空間の形状を考慮すると、最大に近い値をとりやすい領域とそうでない領域が発生している可能性がある。そういった$T$の偏りを考慮すると、実際に最大値をとりそうな領域の周辺で解像度を上げるべきかもしれない。
- もとは階層ごとに最大不等式を使ってバウンドしていた。こうすると、階層ごとにチェーンをぶつ切りにして最大値を達成するような代表点の組をその都度選び直していることになるが、どうせならチェーンはつながったままにして、最大を考える作業は最後にまとめてやれば良い気がする。$\sum\_n \sup\_t$よりも$\sup_\t \sum\_n$の方がおそらくましである（下の(4)式も参照）。

というわけで、generic chainingでは、$T\_n$の点の間隔を可変にして、$\sup\_t$という評価を行う作業は最後まで遅延する。

以下で考える$T\_n$は、$T\_0 = \\{ t\_0 \\}$であって、$T\_0 \subset T\_1 \subset \cdots \subset T$となるような列とする。
ただし、間隔を可変にしたとはいえ、要素数が一気に増えすぎるとunion boundが無意味になるという（若干技術的な）事情があるので、
$$
|T\_n|\leq 2^{2^n}
$$
となるようにする。イメージとしては、$|T\_{n + 1}| \approx |T\_n|^2$というペースで要素数を増やす[1]。
$\gamma\_2(T,d)$を次のように定義する：
$$
\gamma\_2(T, d) := \inf \sup\_{t \in T} \sum\_{n=0}^\infty 2^{n/2} d(t, T\_n)
$$
ここで、infは上のような$T\_n$の全体にわたってとる。$d(t, T\_n) = \min\_{s \in T_n} d(t, s)$とする。

**定理**
普遍的な定数$C > 0$が存在して、
$$
\mathbb{E}\sup\_t X\_t \leq C \gamma\_2(T, d)
\tag{2}
$$
が成り立つ。

証明は省略するが、要は$T\_n$を固定したときに
$$
\Pr( \sup\_t (X(t) - X(t\_0) >  u \sum\_{n=0}^\infty 2^{n/2} d(t, T\_n)) \leq C \exp(-u^2)
$$
であることを言えばよいのであって、これはsub-Gaussian性(1)とunion boundから出てくる。

とはいえ、結局、"最適な" $T\_n$の選び方をある程度特定してやらないことには(2)のタイト感はよくわからない。
一方で、前述のように$T\_n$を等間隔にしたものがchainingであったから、chaining boundに相当するものは(2)から復元できる。

**エントロピー数** $e\_n(T)$を、
$$
e\_n(T) := \inf\_{T\_n} \sup\_t d(t, T\_n)
\tag{3}
$$
と定義する。ただし、infは$|T\_n| \leq 2^{2^n}$をみたすような有限集合全体にわたってとる。エントロピー数は、$\epsilon$-カバリングナンバーや$\epsilon$-パッキングナンバーの逆関数のようなもので、要素数を固定したときの$\epsilon$だと考えればよい。
(3)でinfを達成するような$T\_n$を選ぶことによって、
$$
\gamma\_2(T, d) \leq \sup\_t \sum\_{n=0}^\infty 2^{n/2} d(t, T\_n) \leq \sum\_{n=0}^\infty 2^{n/2} e\_n(T)
\tag{4}
$$
がわかる。しかし、最右辺は、ある定数$C > 0$が存在して
$$
\sum\_{n=0}^\infty 2^{n/2} e\_n(T) \leq C \int\_0^\infty \sqrt{\log N(T, d, \epsilon)} \dd \epsilon
$$
であることがわかるから[2]、chaining boundが (up-to-constantで) 復元する。

# Generic chainingによる下界

$X\_t$はガウス過程であるとする。この場合はとくに、generic chainingによる**下からの評価**も成立する。

**定理 (Majorizing measure theorem)**
$X\_t$はガウス過程とし、$d$は$X\_t$からきまる$T$上の擬距離とする。普遍的な定数$c > 0$が存在して、
$$
\mathbb{E}\sup\_t X\_t \geq c \gamma\_2(T, d)
$$
が成り立つ。

少々どうでもいいんですが、なぜmajorizing "measure"という名前なのかというと、$\gamma_2$と本質的に同じものを代表点$T\_n$ではなくて$T$上の測度を使って定式化することができ、そちらが先に出てきたという歴史的経緯によるものらしい。"Majorizing measures without measures"という（一体何を言ってるんだという感じのタイトルの）論文が存在するあたり、若干その雰囲気がうかがえる。

# まとめ

このノートでは確率過程のsupについて次のことを書いた:
- 有限個のsub-Gaussian変数の最大不等式 ($\sqrt{\log N}$)
- sub-Gaussian processのchaining bound ($\int \sqrt{\log N(T, d, \epsilon)} \dd \epsilon$)
- generic chaining bound (上界 & ガウス過程の下界)

他にもchaining boundがタイトでなくなる具体例とか、下界が成り立つ理由を書こうと思ったものの、力尽きたのでやめておく。[2]が専門書です。

### 参考文献
[1] J. R. Lee. Notes on Gaussian processes and majorizing measures ([PDF](https://homes.cs.washington.edu/~jrl/mm.pdf))
[2] M. Talagrand. Upper and Lower bounds for Stochastic Processes, Springer, 2014.
[3] M. Talagrand. Majorizing Measures without Measures, The Annals of Probability, 29(1):411--417, 2001.