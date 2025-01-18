---
date: 2025-01-19
aliases: SpikeでMindStormを使う方法
tags: 
title: SpikeでMindStormを使う方法
---
# はじめに
SpikeからMindstormを使えるようにすることで、Mindstormの優れた開発環境を使えるようになります。
# なぜ可能なのか
SpikeとMindstormは中のファームウェアが異なるだけで、ハードウェア自体は同じだからです。そのため、SpikeのファームウェアをMindstormのものに書き換えることで、Mindstormの開発環境を使うことができるようになります。
# 手順
## 必要なソフトウェア
Windowsの場合、ファームウェアのダウングレードにドライバが必要です。
afrel
ダウンロード先： https://zadig.akeo.ie/
手順： https://afrel.co.jp/product/spike/technology-spike/basic/software-basic/54122/#windows

## ファームウェアのダウングレード
まず、手始めにSpike本体のファームウェアバージョンを2に落とします。ウェブアプリで簡単にダウングレードできます。
https://spikelegacy.legoeducation.com/hubdowngrade/#step-1

## Mindstormファームウェアの書き込み
Spikeのファームウェアを2に落としたら、Mindstormアプリを開いてください。すると、ハブOSの更新ができるので、更新するとSpikeでMindstormを使えるようになります。