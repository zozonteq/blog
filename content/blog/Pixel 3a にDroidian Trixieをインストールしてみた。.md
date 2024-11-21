---
title: Pixel 3a にDroidian Trixieをインストールしてみた。
date: 2024-03-26
tags:
  - "#Linux"
  - "#Android"
---

# はじめに

Pixel 3aは2019に発売されたスマートホンで、今となっては昔のデバイスですがカスタムロムなどが豊富です。例えばPixel 3a 用の[Lineage OS](../DB/Software/Software_DATA/Lineage%20OS.md)は現在[[Android]] 14までリリースされていて、今でも最新のOSを使うことができたりします。ほかにも[Ubuntu Touch](../DB/Software/Software_DATA/Ubuntu%20Touch.md)ではほぼすべての機能を使うことができたりし、カスタマイズ性が非常に高いです。

　そんなPixel 3aに[droidian](../DB/Software/Software_DATA/droidian.md) という[debian](../DB/Software/Software_DATA/debian.md)のモバイル移植版をインストールして実際に使ってみました。

# インストール

[droidian](../DB/Software/Software_DATA/droidian.md)のReleaseからインストールしたいバージョンのzipファイルをダウンロードして、[[fastboot]]で焼くだけです。

https://github.com/droidian-images/droidian/releases/tag/droidian%2Ftrixie%2F27

ほかにもUbports Installerからもインストールできます。

インストールする前にpq3b.190801(Android 9)というバージョンにする必要があります。

初期パスワードは1234になっています。

# Ubuntu Touchとの違い

## Linux感

[droidian](../DB/Software/Software_DATA/droidian.md)は[[Ubuntu Touch]]と比べるとより[Linux](../DB/Software/Software_DATA/Linux.md)感があると感じました。[Ubuntu Touch](../DB/Software/Software_DATA/Ubuntu%20Touch.md)でデスクトップアプリケーションをインストールするとなると、aptからのインストールではなくlibertineというもので仮想の環境を用意してそこにインストールするといったようになります。Droidianの場合はaptから直接インストールできます。

## デスクトップ環境

[droidian](../DB/Software/Software_DATA/droidian.md)ではPhoshと呼ばれる[Gnome](../DB/Software/Software_DATA/Gnome.md)をベースとしてモバイル向けにカスタマイズされたDEを使っています。[Gnome](../DB/Software/Software_DATA/Gnome.md)は20年前から開発されているデスクトップ環境ということもあり、それをベースとするPhoshも使いやすいと感じました。

# 動く機能と動かない機能

たいていの機能は動作します。不安定だったり注釈が必要な点だけ下にまとめました。

## イヤホン・ヘッドホンが動かない

[droidian](../DB/Software/Software_DATA/droidian.md)の公式サイトにはサポートと書いているのですが完璧に動作しません。ジャックにイヤホンを差し込んだ時に一瞬だけ音はなるのですが、すぐにスピーカーに切り替わってしまうので実質使えません。

## 内臓カメラ

最新のTrixieでは動きます。ですがひとつ前のDroidian Bookwormだとカメラは正常に動作しません。

## 4G

Povoのsimを入れAPNの設定をしてみましたが電波を拾うことができず動作しませんでした。いろいろ設定変えても無理でした。

ほかのsimなら動くかもしれないです。

## USB

OTGケーブルなどを使ってキーボードやマウスなどを使うこともできます。キーボード＆マウスを使うときは設定からディスプレイに行って、画面拡大度を200%や150%などにすると操作しやすくなると思います。

## 指紋

設定→指紋認証から登録できます。登録することでスマホのロック解除画面で指紋認証が使えるようになります。

ちなみに設定は少し癖があります。

## GPS

大まかな位置しかわかりません。

# その他

## バッテリー消費

AndroidOSと比べるとDroidianのほうが若干バッテリーの消費が早く感じます。一応バッテリーの設定もあって、設定＝＞電源の下にあるBatman Optionから省電力にするための設定がいろいろあります。

## スクショ

gnome-screenshotを使ってスクショ取れます。画面全体のスクショのみ動作します。ウィンドウ毎のスクショは部分スクショなどは動作しません。

```shell
sudo apt update && sudo apt install gnome-screenshot
```

## Waydroid

[Waydroid](../DB/Software/Software_DATA/Waydroid.md)をインストールすることで、[droidian](../DB/Software/Software_DATA/droidian.md)上でAndroidアプリケーションを実行できるようになります。[Waydroid](../DB/Software/Software_DATA/Waydroid.md)がハードウェアに直接アクセスできるようにする設定があり、それを使うことで[Waydroid](../DB/Software/Software_DATA/Waydroid.md)でNFCやGPSなども使うことができるようになります。

### インストール

設定=>Waydroidから「Install Waydroid」を押すことでインストールが開始します。

その後、Image TypeからVanillaかGAPPSを選択してインストールします。