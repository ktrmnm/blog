---
title: Generic Chaining (2)
date: 2016-03-25 20:07:49
categories:
- 2016/3
tags:
- 学習理論
- 経験過程論
---
# 連鎖 (Chaining)
[前回](http://ktrmnm.github.io/blog/2016/03/23/20160322-generic-chaining-1/)の続き。

このノートの落とし所としては次のようなものを想定している：
- 確率過程の最大値をバウンドするためのchainingという技術がある
- chainingで得られるバウンドが非最適な場合がある
- generic chainingという技術を使うとそのギャップが埋まる

でも今回は、chainingについて書いたら力つきて終わりそうだ。

# 距離エントロピー
$(T, d)$ を擬距離空間とする。$d$ で $T$ の大きさをはかり、それが $T$ 上の確率過程の最大値に与える影響が知りたいのだった。

大きさのはかり方としてここで想定しているのは、
- 半径 $\epsilon$ の球が最低いくつあれば $T$ を覆うことができるか ($\epsilon$-netの最小数)
- 半径 $\epsilon$ の球を最大いくつ $T$ の中に押し込めるか ($\epsilon$-packingの最大数)

といったもので、前者を**被覆数** (covering number)、後者を**パッキング数** (packing number)という。

<!-- more -->
ちゃんと定義を書く。

$(T, d)$ の$\epsilon$-被覆数 $N(T,d,\epsilon)$ とは、$t\_1, \ldots, t\_n \in T$ が存在して、$T \subset \bigcup\_{i=1}^n B(t\_i, \epsilon)$となるような最小の $n$ のこととする。ただし、$B(t, \epsilon)$は$t$を中心とする$\epsilon$-球である。

$(T, d)$ の$\epsilon$-パッキング数 $D(T,d,\epsilon)$ とは、$t\_1, \ldots, t\_n \in T$ が存在して、各$i \neq j$ について$d(t\_i, t\_j) > \epsilon$ となるような最大の $n$ のこととする。

実は、覆うのも詰めるのもオーダーの上では違いがなくて、
$$
N(T, d, \epsilon) \leq D(T, d, \epsilon) \leq N(T, d, \epsilon/2)
$$
が成り立つ。これからの議論では定数倍の違いは気にしないので、被覆数に関して成り立つことはパッキング数でも成り立つし、逆も言える。必要に応じて便利な方に取り替えることができる。
（注）上の不等式の理由：
最大の$\epsilon$-packingは$\epsilon$-netにもなっていなければならないので左の不等式が成り立つ。
右については、$\epsilon/2$-netをひとつ固定すると、それぞれの球のなかには互いに$\epsilon$より離れた点を$1$点以上含むことができない。よって別の$\epsilon$-packingがあるとすれば、最大でもそれぞれの球にひとつずつ収まることしかできない。

なおこの界隈では、これらの対数をとった$\log N(T, d, \epsilon)$とか$\log D(T, d, \epsilon)$といった量をmetric entropyと呼ぶことがある。

# Dudley積分
$T$ 上の確率過程 $X\_t$ が $d$ についてのsub-Gaussian processであるとは、増分$X\_t - X\_s$がパラメータ$d(s,t)$のsub-Gaussianであることをいう:
$$
\mathbb{E} e^{\lambda(X\_t - X\_s)} \leq e^{\lambda^2 d(s,t)^2 /2}.
$$

結論からいうと、sub-Gaussian processの最大値について次のようなことが言える。([Dudleyの定理](https://en.wikipedia.org/wiki/Dudley%27s_theorem))

**定理**
*$(T,d)$は擬距離空間で、$X\_t$は$d$についてのsub-Gaussian processであるとする。
次ような積分が存在したとする:*
$$
\int\_0^\infty \sqrt{\log N(T,d,\epsilon)} \dd \epsilon < \infty.
\tag{1}
$$
*このとき、*
$$
\mathbb{E}\sup\_{t \in T} |X\_t| \leq \mathbb{E}|X\_{t\_0}| + 4\sqrt{2} \int\_0^{\mathrm{diam}T/2} \sqrt{\log 2 N(T, d, \epsilon)} \dd \epsilon
\tag{2}
$$
*が成り立つ。ただし$t\_0 \in T$は任意の点である。また、*
$$
\mathbb{E}\sup\_{t \in T: d(s,t)\leq \delta} |X\_t - X\_s| \leq (16\sqrt{2} + 2) \int\_0^{\delta} \sqrt{\log 2 N(T, d, \epsilon)} \dd \epsilon
\tag{3}
$$
*が成り立つ。*

これらを示す技術がchainingと呼ばれているもので、そのため不等式自体もchaining boundとか呼ばれたりする。(2)と(3)の違いは、(3)の方はパスの一様連続性の度合い
$$
\sup\_{t \in T: d(s,t)\leq \delta} |X\_t - X\_s|
$$
を$\delta$の関数として評価しているということだ。

これは個人的体験なのだが、機械学習を勉強していたつもりが上のような積分に初めて遭遇したときは心底ぎょっとした。なんじゃこりゃ感がある。カバリングナンバーを$\epsilon$で積分するって一体何事？と悩まざるを得ない。

なんじゃこりゃ感の正体だが、**有限個のmax評価を次々に連鎖** (chaining) したらこうなったのだ、と言える。

[前回](http://ktrmnm.github.io/blog/2016/03/23/20160322-generic-chaining-1/)も述べたが、代表点として有限$\epsilon$-netをひとつ固定したとき、それらの上での $\max |X\_t|$ の期待値は $\sqrt{\log 2N(T, d, \epsilon)}$ に比例する量で押さえられる。ここでのアイデアは、$\epsilon$をどんどん細かくして代表点を増やしていけば、最終的には$T$全体でのsupをカバーできるというものだ。そこで細かくする方法に少々工夫があって、その最中の評価を足しこんでいったものが(1)(2)(3)のような積分ということになる。

連鎖っぽさを具体的に感じるために、(2)の方を大雑把に示そうと思う。

## Chainingによる証明
### 1. 前置き
$\mathrm{diam}T = 0$ または (1) の積分が $+\infty$ のときは無意味なので、$\mathrm{diam}T < \infty$ とする。

距離$d$および$X\_t$を等倍にスケールして、$\mathrm{diam}T < 1$としても問題が生じないのでそうする。

また、とくに $T$ は全有界だが、$T$ 全体ではなく、稠密な加算集合 $T\_0$ の上で (2) を示すことにする（下の可測性の補足も参照）。任意の有限集合 $S \subset T$で (2) を示すことを目標にする。そうすれば、$S\_n \uparrow T\_0$ の極限をとれば単調収束定理より $T\_0$ でも成り立つ。

### 2. Chainの構成
$S$ を固定する。$k\_1$ を十分大きくとって、$S$の2点どうしの距離で最も近いところより長さ $2^{-k\_1}$ が短くなるようにとれば、$B(t, 2^{-k\_1})$ は $S$ の点を高々1つまでしか含まない。
代表点の集合 $T\_k$ を次のようにつくる。
- $T\_{k\_1} = S$
- $1 \leq k \leq k\_1$ に対して、$T\_k$ を最小$\epsilon$-netとする
- $T\_0 = \\{ t\_0 \\}$
絵を描いてみた。
{% asset_img "chaining_grid.png" "Grid" %}

$\\{ t\_0 \\}$ の1点集合からはじまって、半径を半分ずつに縮めたnetをとっていく。$k$番目のレイヤーには$N(T, d, \epsilon)$個の点があって、最後の$k\_1$番目のレイヤーだけはそれぞれのグリッドに$S$の点が1個以下収まっている。
絵心がないので「四角く」描いているけど、$T$ は有限次元でなくてもいいし、$B(t, \epsilon)$ たちはdisjointでもない。

次に、各 $s \in S$ に対して、$T\_k$ への射影 $\pi\_k(s)$ をひとつ定める。
基本的には"近いもの"を選ぶ。つまり、ある $t\_k \in T\_k$に対して $s \in B(t\_k, 2^{-k})$ であれば、$\pi\_k(s) = t\_k$ と定める。$T\_k$ は $2^{-k}$-netになっているはずだから1つ以上は存在するが、もし2つ以上該当する場合は適当に一方を選ぶ。
ただし、$\pi\_k(s) = \pi\_k(s^\prime)$ ならば、$\pi\_{k-1}(s) = \pi\_{k-1}(s^\prime)$ となるようにする。

$\pi\_k(s)$ の行き先をすべて図示してみると、下のようにツリー状になる。

{% asset_img "chaining.png" "Chaining" %}

2つの $s \neq s^\prime$　について、$\pi\_k(s) = \pi\_k(s^\prime)$ というのはチェインが「合流」するということだが、そこから先は終点の $t\_0$ に至るまでずっと同じチェインの上に乗っている。

~~今後も個人的に使いそうなので図を描いてみたが、大変だった……~~

### 3. 最大値評価
上の図で、チェインの作り方より、
$$
d(\pi\_k(s), \pi\_{k-1}(s)) \leq 2^{-(k-1)}
$$
である。$X\_t$はsub-Gaussianだったので、これは
$$
\left[
\mathbb{E}(X(\pi\_k(s)) - X(\pi\_{k-1}(s)))^2
\right]^{1/2} \leq d(\pi\_k(s), \pi\_{k-1}(s)) \leq 2^{-(k-1)}
\tag{4}
$$
を意味する ($X(t) = X\_t$)。

一方、ツリー状に配置したおかげで、第$k$層と$k-1$層を結ぶ青い線は高々 $|T\_k| = $N(T, d, 2^{-k})$ 種類しかない。したがって、最大不等式より

$$
\mathbb{E}\max\_{s \in S} |X(\pi\_k(s)) - X(\pi\_{k-1}(s))|　\leq 2^{-(k-1)} \sqrt{2 \log 2 N(T, d, 2^{-k})}
$$
となる。

よって、
$$
\begin{align}
\mathbb{E}\max\_{s \in S} |X\_t - X\_{t\_0}| 
& \leq \sum\_{k=1}^{k\_1} \mathbb{E}\max\_{s \in S} |X(\pi\_k(s)) - X(\pi\_{k-1}(s))| \\\\
& \leq \sum\_{k=1}^{\infty} 4 \times 2^{-k-1} \sqrt{2 \log 2 N(T, d, 2^{-k})} \\\\
& \leq \sum\_{k=1}^{\infty} 4 \int\_{2^{-k-1}}^{2^{-k}} \sqrt{2 \log 2 N(T, d, \epsilon)} \dd \epsilon \\\\
& = 4 \int\_{0}^{\mathrm{diam}T /2} \sqrt{2 \log 2 N(T, d, \epsilon)} \dd \epsilon.
\end{align}
$$

これで (2) が示された。

えっ。

**すげえ。**

要するに、増分のsub-Gaussian性（より一般にはOrliczノルムでのLipschitz性 [3]）から、(4) 式のように解像度を細かくしていくと分散が上手くコントロールされる。で、解像度の上げ方をツリー状にまとめ上げることで、各層で考えるべき候補の数を減らしている、というところがポイントである。

### 空間が大きいと最大値が暴れる
上で描いた図では2次元なので、半径 $\epsilon$ を $1/2$ にしたときに $N(T, d, \epsilon)$ は $4$ 倍程度にしかならない。

しかし、一般に $T$ が無限次元だと、カバリングナンバーはもっと急速に増える可能性がある。そして、あまりに爆発的に増えすぎるとエントロピーの積分が収束しないため、最大値がバウンドできないことになる。これが、添字空間が大きすぎると最大値が発散することのイメージである。

# Chainingだと物足りないケース
Chainingのおかげで、要素数無限大でも最大値がバウンドできるようになってハッピーだ。

次に疑問になってくるのは、エントロピーで得られる (1) のようなバウンドが一体どれくらいタイトなのか、ということだ。
実は、実用上使いそうであるにもかかわらず、あまりタイトではない例が知られている。具体例を書こうと思ったが、スペース上の都合で次回にまわす。

そこで**generic chaining**である。

とくに、$X\_t$ が ガウス過程の場合は
$$
\frac{1}{C}\gamma\_2(T, d) \leq \mathbb{E}\sup\_{t \in T} X\_t \leq C \gamma\_2(T, d)
$$
のように**下界もgeneric chainingで書ける**ということがわかっていて (majorizing measure theorem)、正真正銘タイトなバウンドである。


## 補足：GC集合
Dudley積分の偉いところは、supの期待値の有界性だけでなくて、標本のごと連続度も表現してくれていることだ。
どういうことかというと、確率1で
$$
\sup\_{s,t \in T} \frac{|X\_t - X\_s|}{\int\_0^{d(s,t)} \sqrt{2 \log 2 N(T, d, \epsilon)} \dd \epsilon} < \infty
$$
が成り立つ（[1] Theorem 2.3.8, [2] Theorem 2.3.6)。ただし、こっちを示す方がだいぶしんどい。

ガウス過程に関して、$\sup\_t X\_t$ が有界になるような $T$ をGB集合、さらに$d$-一様連続になるような $T$ をGC集合という[2]。つまりDudley積分はGC集合であるための十分条件を与えてくれる。

## 補足2：Donsker性の判定
さらにさらに、経験過程 $\sqrt{n}(P\_n - P)$ が標本連続なガウス過程に法則収束してくれるか否か (一様中心極限定理！) というのは経験過程論における大問題である[2]。もし収束するなら、その添字集合は**Donskerクラス**であるという。
これを判定するにはどうすればいいかというと、結局のところ添字集合の大きさが問題になる。そもそも収束先のガウス過程にとってGC集合になっていないといけないのだから、Dudley積分のような、「標本連続性をコントロールする何か」を見てDonskerクラスかどうか判定できるんじゃないかという気がする。それはだいたい正しいのだが、経験過程の色々なことを一言で説明するのは「無理！」という感じなので[1]や[2]を参照。

## めんどくさい補足：可測性
可測性の話を完全に無視した。これからも無視する。

どういうことかというと、可測関数の非可算個のsupは可測とは限らないのだから、本当は$\sup\_t X_t$は確率変数とは限らないのだ。しかし、(1)の積分が存在するケースでは$T$はそもそも全有界で、$X\_t$にはa.s.で一様連続なパスが出てくるバージョンが存在するので、supも可算集合の上で考えればよいから実質的に問題にならない[1]。あるいは、[3]や[4]ではsupの解釈そのものを、はじめから $\sup\\{ \mathbb{E}\sup\_{t \in F} |X_t|: F は有限集合\\}$ と考えているようだ。

これに限らず、興味ある対象が非可算supなせいで経験過程関係の本 (とくに[2]！) には可測性の話がうじゃうじゃ出てくる。私の近所では「初見殺しだ」ということで話題だ。

### 参考文献

[1] Giné and Nickl. Mathematical Foundations of Infinite-Dimensional Statistical Models, Cambridge University Press, 2015.
[2] R. M. Dudley. Uniform Central Limit Theorems (2nd edition), Cambrige University Press, 2014.
[3] M. Ledoux and Talagrand. Probability in Banach Spaces, Springer, 1991.
[4] M. Talagrand. Upper and Lower Bounds for Stochastic Processes, Springer, 2014.