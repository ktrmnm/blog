---
title: 機械学習アルゴリズムをテストしたい
tags:
  - 機械学習
  - C++
  - TDD
categories:
  - 2018/3
date: 2018-03-04 03:30:00
---


# 動機

論文などを読みながら機械学習のアルゴリズムを実装していると、

- 出力が浮動小数点数の巨大な配列である
- 出力がランダムに決まる
- テストケースに対する正しい出力を手計算するのが不可能
- そもそもアルゴリズムを自分が理解してない（死）

とかの理由で、書いたコードが正しいかどうか全然わからないです。そして、だいたい正しくないです。

本当は小さい単位でテストしながら書きたいのですが、何がベストプラクティスなのかよく知らないし、とりあえず試したことのメモを蓄積することにしました。

# 書いたもの（随時更新）

- [Google Test勉強録 (1) CMakeでのビルド](https://qiita.com/ktrmnm/items/667a7b7c93cd3fb78419)
- [Google Test勉強録 (2) アルゴリズムのテストを書く](https://qiita.com/ktrmnm/items/a9c9d9eed9ea2d9cebdc)
