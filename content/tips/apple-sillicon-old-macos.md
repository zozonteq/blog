---
date: 2024-12-03
aliases: Apple Silliconで古いMacOSを動かす（Mojave）
tags: 
title: Apple Silliconで古いMacOSを動かす（Mojave）
---
# はじめに
[UTM](../DB/Software/Software_DATA/UTM.md)を使ってAppleシリコン上で[MacOS](../DB/Software/Software_DATA/MacOS.md)のMojaveを動かしてみます。

# 環境

| UTM   | v 4.6.2(104) |
| ----- | ------------ |
| macOS | 15.1         |
| CPU   | M3           |
| Ram   | 16GB         |
# Mojaveイメージファイルの取得
このスクリプトを参考に取得しました。
https://gist.github.com/ahmed-alamir/264156e166401afb9fe0669cf06c2a61
- OSが新すぎたり、Apple Silliconだとうまく動作しないっぽいです。（M3、15.1はエラーが出て無理でした）
- MBA 2012、Bigsurで抽出できました
- うまくいくと`Mojave.iso.cdr`というファイルがデスクトップ上に生成されます
# 仮想マシンの作成
AppleSilliconとMojaveの対応アーキテクチャが異なりますので、「Emulate」を選択します。
![](../../../Pasted%20image%2020241203090330.png)
WindowsでもLinuxでもないので、Otherを選択します。CD/DVDが選択できるので、そこで先ほど抽出した`Mojave.iso.cdr`を選択します


![](../../../Pasted%20image%2020241203101645.png)
