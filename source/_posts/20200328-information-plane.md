---
title: (勉強メモ) Information bottleneck (2)
tags:
  - 機械学習
  - 情報理論
  - 個人用メモ
categories:
  - 2020/3
date: 2020-03-28 18:00:00
---

[前回](http://ktrmnm.github.io/blog/2020/03/22/20200322-information-bottleneck/)に引き続いてinformation bottleneck (IB) 関連の論文を読んでいました。

特に、information plane (IP) という図の上で深層学習の挙動を説明する議論について調べていました。ひとまず現状把握という感じのメモで、細かいところは読んでいません。

# Information plane

## 発端

深層学習の挙動をinformation bottleneckの言葉で解析する研究の発端は、以下の2つだと思われる。

- [1] Tishby and Zaslavsky (2015). Deep Learning and the Information Bottleneck Principle. [(URL)](https://arxiv.org/abs/1503.02406)
- [2] Shwartz-Ziv and Tishby (2017). Opening the Black Box of Deep Neural Networks via Information. [(URL)](https://arxiv.org/abs/1703.00810)

Information bottleneckでやりたいことを直感的に述べると、ある入力信号Xを圧縮するに当たって、別の信号Yの情報をなるべく喪失しないようにしつつ可能な限り小さく圧縮するというトレードオフを考えるというものであった。分類問題のように、特徴量Xを入力としてラベルYを予測したい状況を考えると、この思想に自然に行き着く。[1] では、DNNによる分類の挙動をIBの言葉で解析した場合に、どんなことが言えてほしいかという議論をしている（具体的な解析というより、解析方針の提言みたいな感じである）。IBの議論より、中間層表現をLとすると、入力Xの情報が中間層においてどの程度圧縮されているかは $I(X, L)$ がどの程度小さいかに対応している (厳密には違うけどrate distortion theoryを意識するとそう思うこともできる)。また、中間層LにおいてラベルYの情報がどの程度残存しているかは、$I(L, Y)$ がどの程度大きいかに対応している。そこで、DNNにおいてある時点での $I(X, L)$ と $I(Y, L)$ を縦軸と横軸にプロットすることによって、現在のDNNの状態がIBのトレードオフにおいてどの位置にあるかを可視化することができるでしょう、ということである。この図をinformation planeと呼んでいる。

[2] は、[1] の提言に基づいて具体的な解析を行った初めての論文で、SGDのエポックを追うごとにinformation plane上ではどの位置にいるかを可視化した。主な主張として、information plane上で、SGDで訓練したDNNの典型的な挙動は下図のようになると言っている。 (この図は [4] から借りてきた。実際の実験を表したものではなく、概念図である。)

{% asset_img "ip_ideal_geiger2020.png" "Figure from https://arxiv.org/abs/2003.09671" %}

横軸が符号の大きさ (レート) に対応する $I(X, L)$ なので、左に行くほど圧縮率が高い。縦軸は $Y$ に関する情報の残存量であり、上に行くほど $Y$ に関する情報は多く保持している。よって直感的には、よい汎化のためには、「左上」のほうに向かうのが嬉しいということになる。[2] は、DNNをSGDで訓練するとき、次の2つのフェーズがあるということを主張している。

1. 初期のエポックはERMフェーズで、データに対してDNNをフィッティングさせることに費やされる。この間、圧縮率は上がらない ($I(X, L)$ が増える) ので、IP上では「右上」に向かっていく。
1. 十分に訓練誤差が下がった段階で、圧縮フェーズに移行する。圧縮フェーズでは、$Y$ に関する情報残存量 $I(Y, L)$ はあまり変えないが、中間表現 $L$ の余計な部分が削ぎ落とされて $I(X, L)$ は下がる。

[2] では、tanh活性化関数をもつNNによる実験で、実際にこのような図を描いている。こういった実験を通して、この論文では次のような主張をしている。

- SGDによるNNの訓練では、ERMフェーズは相対的に短く、ほとんどのエポックは圧縮フェーズに費やされる
- SGDの軌道は、初期段階では訓練誤差を減らすべく速く遷移する (drift phase)。十分に訓練誤差が小さくなると、勾配はとても小さくなって、SGDのノイズに起因した拡散過程的な動きが支配的になる (diffusion phase)。Information plane上での圧縮は ($I(X, L)$ の減少はdiffusion phaseによって起こっている。(こういう感じのストーリーは、SGDをdiffusionとして解析する他の研究でも言われている気がする。[ICAの論文](https://arxiv.org/abs/1808.09642) とか)
- NNの隠れ層を増やすと、compressionが速くなって訓練が速くなる (必要なepoch数が減少する)
- DNNはIBの意味での限界に近いところに収束できる

## 批判

これらの主張は、成り立つとすれば面白いが、一般のDNNの訓練で成り立つと結論づけてしまうには強すぎると思われた。で、このあたりのストーリーが果たして本当なのかについて、後続研究で議論が続いているように思われる。例えば、以下の [3] では、上の主張はどれも必ずしも成り立たないということを述べている。

- [3] Saxe et al. (2018). On the Information Bottleneck Theory of Deep Learning. [(URL)](https://openreview.net/forum?id=ry_WPG-A-)

[3] では、次のようなことが指摘されている。

- 圧縮フェーズの存在はtanhのように両側がsaturateした活性化関数に特有の現象なのではないか？ 実際に、ReLU DNNにおいて実験してみるとcompression phaseのような挙動が見えなくなったり (IP上では一貫して右肩上がりになる)、linear networkに関してはcompression phaseがないことが示せる。
- 圧縮フェーズのためにrandomness (diffusion phase) が必要とは限らない。Batch gradient descentでもcompression phaseのような挙動が見えることがある。

[3] は論文だけでなくて、[Openreview](https://openreview.net/forum?id=ry_WPG-A-) 上で [2] の著者と直接議論が進められているので、そちらも眺めた。いずれにせよ [2] も [3] も何かを結論づけるにはまだ早いが、重要な議論であるので後続研究を続けよう、というのが [3] の採択理由でも述べられている。

出力 $Y$ は分類問題においては離散変数であるが、入力 $X$ や中間層 $L$ は (計算機上で実装しているとはいえ) 連続変数とみるのが自然とも考えられる。実験では、相互情報量は厳密計算しているわけではなく、空間をbinに区切ったり、カーネル密度推定量ベースの推定量を使ったりして推定を行っている。実際、この推定量の選択が実験結果 (＝information plane上での挙動の違い) に影響しうるよね、ということがopenreviewでも議論されている。タイミングのいいことに、先週、相互情報量の推定量の選択にもフォーカスしたinformation planeのサーベイ論文が公開された。

- [4] Geiger (2020). On Information Plane Analyses of Neural Network Classifiers -- A Review. [(URL)](https://arxiv.org/abs/2003.09671)

このサーベイ論文をみると、[3] 以降にもinformation planeの後続研究がたくさん出ていることがわかる。下の表が立派で、unpublished workも調べ上げて、(i) NNのアーキテクチャ (ii) 活性化関数の選択 (iii) 訓練アルゴリズム (iv) データセット (v) 相互情報量の推定手法の組み合わせに基づいて、compression phaseが観察されたかどうかについてまとめられている。

{% asset_img "table_geiger2020.png" "Table from https://arxiv.org/abs/2003.09671" %}

まとめると、information planeを利用した研究において、現状では [1] や [2] で期待されたようなストーリーが成り立っているとは言い切れない。一方、思想的には色々と面白い部分があるし、依然として可能性は感じるので、それなりの数の研究者が取り組んでいるのだと思う。

## 感想

ちなみに、[4] に書いてあって、個人的に気になる文章はこちら:
Our analysis suggests that compression visualized in information planes is not information-theoretic, but is rather compatible with geometric compression of the activations.

確かに、相互情報量ってgeometryの情報を陽に考慮した概念ではないので、完全にinformation-theoreticな議論にこだわらずに、幾何学的に何が起きてるのか考えるのは面白そう (もともと符号理論にしてもレート歪み理論にしても離散的なビット列のようなものに圧縮するための理論である)。相互情報量を推定するときにbinningやKDEを使うと、例えば近いセルをひとまとめにするという意味でgeometryの情報は暗に使っているのだが、そのへんの働きをクリアにしたいよねというのは思った。