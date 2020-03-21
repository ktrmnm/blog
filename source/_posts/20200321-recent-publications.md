---
title: 最近出した論文 (劣モジュラ正則化とかGANとか)
tags:
  - 統計学
  - 機械学習
  - 日記
date: 2020-03-21 15:46:55
---

ブログを約2年放置してしまいました (前回は2018/4/30)。

この2年ほどのあいだに、自分の環境 (職場、家庭、学位) がいろいろ変わって大変でした。しかしそろそろ仕事以外の勉強も再開していきたいので、リハビリがてら色々書いていこうと思います。

久しぶりすぎて何から書き始めるか迷ったのですが、自分が関わった論文が直近1年の間に何本か出たので、それらについて書きます。

# 劣モジュラ正則化

劣モジュラ正則化 (submodular regularization) というテーマで論文を2つ書きました。これは自分の博士課程の研究です。

劣モジュラ正則化とは何かというと、LassoとかGroup lasso, Fused lassoのような凸正則化法を拡張した概念です。これらの正則化法は、推定したいパラメータに対する構造的スパース性 (structured sparsity) を誘導するものです。例えば、線形回帰におけるLassoは

$$
  \min_{\theta \in \mathbb{R}^p} \frac{1}{2} \| y - X \theta \|^2_2 + \lambda \| \theta \|_1
$$

というような凸最適化問題の解として定義されるものですが (※記号の説明は略)、ここでL1正則化項はパラメータ $\theta$ に対するcoordinate sparsity (いわゆる普通のsparsity) を誘導します。しかし、もっと「構造のあるスパース性」を考えることもできます。例えば、generalized fused lassoという手法では

$$
  \sum_{(i, j) \in E} |\theta_i - \theta_j|
$$

という正則化項を利用しますが、これは与えられたグラフ $G = (V, E)$ 上で区分的に定数となるような性質を誘導します。

こういった正則化項が、実は [劣モジュラ関数](https://en.wikipedia.org/wiki/Submodular_set_function) と関係があるということを指摘した [2010年の論文](https://papers.nips.cc/paper/3933-structured-sparsity-inducing-norms-through-submodular-functions) があります。具体的には、Lasso, Group lasso, Fused lassoといったような代表的な手法も含む比較的広いクラスの正則化項が、劣モジュラ関数の凸緩和 (Lovász extension) として書けます。

このテーマについて本も出ているので、興味があればそちらを参照するのがわかりやすいかと思います。

- 河原, 永野. [劣モジュラ最適化と機械学習](https://www.kspub.co.jp/book/detail/1529090.html)
- F. Bach. [Learning with Submodular Functions: A Convex Optimization Perspective](https://www.nowpublishers.com/article/Details/MAL-039)

こういった一般化をするひとつの利点として、proximal operator ([以前のブログ](http://ktrmnm.github.io/blog/2018/04/30/201804-proximal/) でも書きました) の計算が劣モジュラ関数最小化と関係のあるアルゴリズムで解けるという話があります。というわけで、劣モジュラ正則化というのは、数理統計 (Lasso)、機械学習 (structured sparsityのモデリング)、離散最適化 (劣モジュラ関数最小化) のインターセクションにある研究テーマになっているというわけです。自分が博士課程に進学した当初、こういう分野横断的な話が面白そうだなと思って勉強してましたが、大学で [劣モジュラ関数の教科書](https://www.elsevier.com/books/submodular-functions-and-optimization/fujishige/978-0-444-52086-9) (すごく難しい) などを輪読したりしているうちに最適化の関係ない勉強が楽しくなり、どんどん脱線して収拾がつかなくなってきました。博士課程あるあるっぽい話ですが、皆さん気をつけましょう (?)

自分の研究ですが、以下のことをしました。（圧縮した説明）

1. Degrees of freedomの研究 ([論文はこちら](https://www.sciencedirect.com/science/article/pii/S0047259X18305906?via%3Dihub) ): Degrees of freedomというのは、線形回帰モデルの推定量に対して定義される「実効パラメータ数」のような概念です。あるいは、fixed design regressionにおいてバイアスバリアンス分解したときの、バリアンスに相当する部分です。具体例としては、[Mallows' Cp](https://en.wikipedia.org/wiki/Mallows%27s_Cp) やAICによるモデル選択では「変数の数」が罰則になりますが、実際にGaussian noiseの設定でOLSのdegrees of freedomを計算すると変数の数が出てきます。一方、degrees of freedom自体は非線形推定量に対しても定義することができて、例えば[Lassoでは解の非ゼロパラメータ数と一致する](https://projecteuclid.org/euclid.aos/1194461726) ことなどが知られています。この研究では、劣モジュラ正則化に対してdegrees of freedomを統一的に計算する公式を導出しています。とくに、計算するアルゴリズムが劣モジュラ関数の選択に依らず、「解の成分の中でユニークな値を数える」という方法でdegrees of freedomが計算できます (現実にはソートの計算量があればOK)。こういった「アルゴリズム的な性質」が劣モジュラ正則化に特有なところで、一般の凸正則化ではこうなるとは限らないのが面白いところです。
1. 「区分単調信号推定」の研究 ([論文はこちら](https://arxiv.org/abs/1905.01840) ): [Nearly-isotonic regression](https://www.stat.cmu.edu/~ryantibs/papers/neariso.pdf) という手法があり、それについて解析した論文です。単調関数の推定手法で [isotonic regression](https://en.wikipedia.org/wiki/Isotonic_regression) という歴史ある手法があるのですが、それを正則化法に拡張した版がnearly-isotonic regressionです。もともと「単調増加である」というハードな制約があったところを、正則化法にすることによってソフトな制約として扱っているので、ある程度構造的なmisspecificationがあることも許されます。[Rのパッケージ](https://cran.r-project.org/web/packages/neariso/index.html) もあります。自分の研究の文脈としては、実はこのnearly-isotonic regressionは劣モジュラ正則化の一例になっているので、その事実を利用してアルゴリズムを導出したり、上の研究成果を使ってdegrees of freedomを導出したりできます。この論文としては、区分単調信号 (piecewise monotone signal) の推定という設定を考えて、その状況下でのリスクバウンドを導出したりしています。

博士課程は、(つい最近の生々しい出来事なのであまり整理して語れないですが) まあまあつらかったです。能力的な意味では、自分の場合はコードを書いたり英語での論文ライティングがボトルネックになっていると感じていたので、たくさん練習しようと試みました。が、勉強とか「素振り」ばかりしていると、その間アウトプットが全然ないので、普通にどんどん心が折れていきます…。単著論文中心の研究生活だったのですが、とはいえ、自分に対する報酬の設計はうまくやったほうが精神衛生上よかったなと思いました。また、研究の一貫で劣モジュラ最小化のCython実装とかをかなり時間をかけて書いてました。これは、残念ながら論文成果にはほとんど繋がっていないのですが、劣モ最小化のプログラムは世の中にあまり存在しないはずなので、せめて整理してGithubにでも上げておきたい…が、なかなか時間がとれず、という感じであります。

# GAN

2019年4月に[PFN](https://preferred.jp/ja/)という会社に入社し、業務で研究をしています。依然として論文も書いたりしており、昨年度はインターン生と書いた論文についての[解説記事を会社のブログに書いたりしました](https://tech.preferred.jp/ja/blog/smoothness-and-stability-in-gans/)。これはGANの理論についての研究ですが、こういった複雑なアウトプットを出せる生成モデルにはずっと興味があるので、今後も何かできたらいいなと思っています。ちなみに、論文が採択されたICLR2020はエチオピア (!) で開催されるはずだったのですが、COVID19の影響で現地開催はなくなってしまいました。どうなることやら…