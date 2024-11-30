---
date: 2024-11-29
aliases:
  - Tensorflow.jsでmnist(文字認識）をやってみる
tags: 
title: Tensorflow.jsでmnist(文字認識）をやってみる
---
> [!INFO]
> 執筆中です
# はじめに
先日、Tensorflow.jsを使って手書きの数字の分類モデルを作成したのでその時のメモです！

mnistはtensorflow（Pythonの方）の公式チュートリアル序盤に登場するくらい有名なデータセットで、機械学習初学者にもおすすめです。
## この記事で取り扱うこと
- JavaScript（Node.JS）で機械学習モデルの構築
- parquetファイルの読み取り
- JavaScriptでニューラルの構築とコンパイル
- モデルの精度検証

ニューラルネットや機械学習の細かい仕組みについてはあまり触れられないかもしれません。
## 環境

| 名前      | 値                |
| ------- | ---------------- |
| JSランタイム | node.js v20.18.0 |
| OS      | macOS 15.1       |
# 目的変数と説明変数
まず、目的変数と説明変数についての説明です。機械学習をやる上で必須の知識です。

たとえば、パソコンの値段を推測する機械学習モデルを構築するとします。その際必要な情報としてパソコンのメーカー、CPUの性能、SSDの容量、インストールされているOS...などといった値段を決めるための説明となっている変数（値）のことを、**説明変数**と呼びます。逆にこれらの **説明変数** から予測値として算出される値（予測したい値）のことを **目的変数**と呼びます。

- 目的変数は「結果」であり、説明変数はその「原因は要因」であると考えるとわかりやすいです。
- 機械学習では説明変数から目的変数を適切に予測するのが目的です。
# データセットの用意
- まずは学習に使うデータセットの用意です。データセットはhuggingfaceから、`ylencun/mnist`を利用します。
	- 数字とラベルがセットになっているデータセットです。機械学習入門で人気のデータセットです。
	- トレーニング用 https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/train-00000-of-00001.parquet
	- テスト用 https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/test-00000-of-00001.parquet
- また、今後の作業の簡略化のためにダウンロードや保存などといった処理をまとめました。

ファイルのダウンロード・データセットのダウンロード処理などは以下のようになっています。
```typescript
const fs = require("fs")
const path = require("path")

const DOWNLOAD_DIR = "./downloads"

const download_data = async (file_name,url) => {
  const data_buffer= Buffer.from(await (await fetch(url)).arrayBuffer()); // fetch()でファイルをダウンロード、変数に格納
  fs.writeFileSync(path.join(DOWNLOAD_DIR,file_name),data_buffer); // 同期的にデータを保存
}
const IfFileNotExistThen = (path,cb) => { //ファイルがなければコールバック関数を呼び出す
  if (!fs.existsSync(path)) cb();
}
const PrepareDataset = (datasets) => {
  if(!fs.existsSync(DOWNLOAD_DIR)) fs.mkdirSync(DOWNLOAD_DIR)// downloadsフォルダが存在しなければ作成
  datasets.map(async (dataset) => {
    // データセットダウンロード処理
    IfFileNotExistThen(path.join(DOWNLOAD_DIR,dataset.name), async () => {
      console.log("download:",dataset.url)
      await download_data(dataset.name,dataset.url)
    })
  })
}
```
- `download_data(file_name,url)`引数にファイル名とダウンロード先のURLを持ちます。ファイルを`./download/${file_name}`フォルダに格納します。
- `IfFileNottExistThen(path,cb)`引数にファイルパスとコールバック関数を持ちます。`path`が存在しなかった場合、`cb()`を呼び出します。
- `PrepareDataset(datasets)`データセットの情報を定義したオブジェクト配列を引数に持ちます。引数から受け取った情報を元にファイルをダウンロードします。`./download`フォルダが存在しなければ、新規作成します。
## mnistデータセットをインポートする例

```typescript
const TRAIN_DATA_URL = "https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/train-00000-of-00001.parquet"
const TEST_DATA_URL  = "https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/test-00000-of-00001.parquet"

  const datasets = [
    {
      name: "train.parquet",
      url: TRAIN_DATA_URL
    },
    {
      name: "test.parquet",
      url: TEST_DATA_URL
    }
  ];
  PrepareDataset(datasets);

```
- 呼びだし例です。`datasets`オブジェクト配列を用意して、それを`PrepareDetaset`の引数として渡すことで自動的にデータセットの用意をしてくれます。
- また、`PrepareDetaset`関数はデータセットファイルがすでにある時にはダウンロードを実行しません。そのためデータに変更があった場合は`./downloads`から該当ファイルを削除し再度実行することで更新することができます。
- `dataset`オブジェクト配列は、各オブジェクトそれぞれ`name`(ファイル名)と`url`(ダウンロード先)の値を持ちます。
# データセットのロード
- データセットの用意はできたので、次はデータを読み込んでいきます。
- 今回扱うデータセットはparquetというフォーマット
	- CSVのように行と列を持つ。
- JavaScriptでparquetを扱うために、今回は`@dnsp/parquetjs`ライブラリを使いました。

```javascript
const LoadDataset= async (path) => {
  const reader = await parquet.ParquetReader.openFile(path).catch(r => console.log("Error occured while reading parquet file:",r));
  const cursor = reader.getCursor();

  let buffer = [];
  let record = null;
  while(record = await cursor.next()){
    buffer.push(record)
  }
  return buffer;
}
```
- `async LoadDataset(path)`は引数に対象の`.parquet`ファイルのパスを受け取り、内容を読み込んで返す非同期関数です。
- `await aParquetReader.openFile(path)`でファイルを開きます。
	- `.catch()`エラーハンドラを使うことができます。
- `cursor` は`reader`からデータを読み取るときのカーソルです。ファイルのデータを読み込んでいきます。
- `buffer`は配列で、この非同期関数の返り値です。`cursor.next()`で読み取った値を一時的に`record`変数に格納し、`buffer`配列にプッシュします。
- ファイルからデータを全て読み込み終わったら、`while`ループから抜け`buffer`配列の値が返されます。
## データセットをロードする
```js
const TRAIN_DATA_URL = "https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/train-00000-of-00001.parquet"
const TEST_DATA_URL  = "https://huggingface.co/datasets/ylecun/mnist/resolve/main/mnist/test-00000-of-00001.parquet"

const datasets = [
	{
	  name: "train.parquet",
	  url: TRAIN_DATA_URL
	},
	{
	  name: "test.parquet",
	  url: TEST_DATA_URL
	}
];
PrepareDataset(datasets);
console.log("Loading traindata")
const traindata = await LoadDataset(path.join(DOWNLOAD_DIR,datasets[0].name));
console.log("Loading testdata")
const testdata = await LoadDataset(path.join(DOWNLOAD_DIR,datasets[1].name));

```
- これで`.parquet`形式のデータセットを読み取る関数が完成したので、データセットの用意からロードまでの部分が完成しました。
- 先ほど作成した、`await LoadDataset(path)`を呼び出してデータを取ります。引数にはダウンロードしたデータセットのファイルパスを指定します。
- トレーニング用とテスト用のデータをこのタイミングで両方とも読み込んでいます。
## データの読み取りについて
```javascript
[
	// ...
	{
		image: [00,00,00,34,34,34,67,67,67,...],
		label : "1"
	},
	{
		image: [F0,F0,F0,24,24,34,67,67,67,...],
		label : "1"
	},
	// ...
]
```
- 読み込んだParquetデータの読み込みについてです。
- 今回のmnistデータの場合、上のようなデータ構成になっています。
- `image`: 画像ファイルがバイト列で並べられています。
	- フォーマットはモデルによってバラバラですが、学習しやすいように`bmp`などといったピクセルごとに独立した画像フォーマットを使うことが推奨されます。
	- 説明変数
- `label`:対象となる学習データの答えとなる値です。
	- たとえば今回のデータセットの場合、`image`の手書き画像が「０」を表す画像であれば、`label`の値は０となります。
	- 目的変数
## 試しに0番目のlabelの値を読み取ってみる
```js
PrepareDataset(datasets); // データセットをダウンロード
const traindata = await LoadDataset(path.join(DOWNLOAD_DIR,datasets[0].name)); //トレーニング用のデータをロード
const label = traindata[0].label; // 0番目のラベルの値。
const image_byte = traindata[0].image.bytes; // 0番目の画像の値（バイト列）。
```
たとえばトレーニングデータの０番目を読み込んでみましょう。このような形で各値を読み取れます。
- 画像や動画などののデータファイルを読み込む場合、`.bytes`をつけることでバイト列として読み込むことができます。
- 読み取った値を`console.log`などで出力してみると、どういった値が入っているのかイメージがつくかと思います。
## データセットのデータ形式について
今回のデータセットのファイルは以下のような形式になっています。（`console.log`などで出力するとわかる）
- `image.bytes`（説明変数）
	- 画像サイズは`28px x 28px`
	- `image.bytes`のバイト列の長さが一定ではない。
		- つまり、圧縮されているデータであると推測される
	- 白黒の画像データだが、赤、緑、青（RGB）のデータを持つ
- `label`（目的変数）
	- 答えとなる数が文字列として記載されている
## データセットを機械学習で扱いやすい形式に変換する
そして、以上の条件を満たした扱いやすい形式に変換する
- `image.bytes`
	- image.bytesを`raw`に変換しバイト長を`784`(`28 * 28`)に固定する
	- RGBデータを白黒のグレースケールデータに変換する。
- `label`
	- 数値に変換する
### 画像をrawに変換
```js
const sharp = require("sharp")

const TraindataToBMPs = async (traindata) => {
  return await Promise.all(
      traindata.map(async (v) => {
        const image_bytes = v.image.bytes;
        const bmp_buffer = await sharp(image_bytes)
          .toFormat("raw")
          .toBuffer();
        return bmp_buffer;
      })
    )
}
```
- トレーニングデータを引数から受け取り、それを扱いやすい`raw`に変換するコードです。
- 画像処理ライブラリに`sharp`を使っています。
### RGBデータを白黒のグレースケールに変換する
```js
bmp_buffer = [FF,FF,FF,22,22,22, ...]; //こいつを
bmp_buffer = [FF, 22] //こんな感じに変換する
```
- 先ほど説明した通り、今回のトレーニングデータは白黒のデータです。
- しかし、不要なRGB要素を持っておりそれをグレースケールに変換する作業が必要です。
	- 学習時間の短縮・最適化につながる
```js
const BMPsToGrayscale = async (bmps) => {
  return await Promise.all(
      bmps.map( async(bmp,i) => {
        let buffer = [];
        for(let i = 0 ; i < 28 * 28;i++){
          buffer.push(bmp[i*3] / 255); // 正規化
        }
        return buffer;
      })
    )
}
```
- 前のセクションで作成した`raw`形式のデータを引数のとる。
- グレースケールに変換する
- `255`で割って正規化する。
```javascript
const datasets = [
	{
	  name: "train.parquet",
	  url: TRAIN_DATA_URL
	},
	{
	  name: "test.parquet",
	  url: TEST_DATA_URL
	}
];
PrepareDataset(datasets);
console.log("Loading traindata")
const traindata = await LoadDataset(path.join(DOWNLOAD_DIR,datasets[0].name));
console.log("Loading testdata")
const testdata = await LoadDataset(path.join(DOWNLOAD_DIR,datasets[1].name));

const train_bmps = await TraindataToBMPs(traindata);
const test_bmps = await TraindataToBMPs(testdata);

const train_features = await BMPsToGrayscale(train_bmps);
const test_features = await BMPsToGrayscale(test_bmps);

```
- 上の章で新たに実装した`TraindataToBMPs`と`BMPsToGrayscale`を組み込むと上のようなコードになります。
- まず初めに`TraindataToBMPs`を通して、データセットの画像データを`raw`形式に変換します。
- `raw`に変換しただけだと、グレースケールが施されていなかったり、データの正規化がされていないので`BMPsToGrayscale`を呼び出してそれらを施します。
- これで学習する際に適切なデータ形式に変換することができました。

# 目的変数の用意
- 上のセクションで説明変数の用意はできました。今度は目的変数の用意です。
	- 目的変数は「結果」であり、説明変数はその「原因は要因」
- 今回の場合`label`が目的変数でこれを適切なフォーマットに変換していきます。
- `label`はプリミティブな値なので楽に実装できます。

```javascript
const train_labels = traindata.map(v => Number.parseFloat(v.label));
const test_labels = testdata.map(v => Number.parseFloat(v.label));
```

# テンソルの用意
- データセットの前処理と目的変数・説明変数の用意は終わりました。
- 用意した目的変数と説明変数をテンソルに置き換える処理です。
```typescript
const xs = tf.tensor2d(train_features);
const ys = tf.tensor1d(train_labels, 'float32');
```

# ニューラルの構築
```js
const model = tf.sequential();
model.add(tf.layers.dense({
	units:128,
	activation:"relu",
	inputShape: [train_features[0].length]
}))
model.add(tf.layers.dropout(0.5))
model.add(tf.layers.dense({
	units:64,
	activation: "relu"
}))
model.add(tf.layers.dense({
	units:32,
	activation: "relu"
}))

model.add(tf.layers.dense({
	units: 10,
	activation:"softmax"
}))
```
- 上は構築したニューラルです。今回は受け取った画像から数字を分類するモデルを作成していきます
- まず`tf.sequential()`でモデルの初期化を行なっています
- `dense`層について
	- `dense`層は全てのニューロンが接続されている、完全結合層です
	- `dense`層は以下のような情報を持ちます
		- `units`: ニューロンの数
		- `activation`:活性化関数の指定
		- `inputShape`:入力データの形状
- はじめの層は`dense`層です。
	- ニューロン数（units）128個、活性化関数はReLU(Rectified Linear Unit)が指定されています。
	- また、`inputShape`にはトレーングデータの長さが指定されています。今回の場合だとtrain_features[0].lengthは`28 * 28 = 784`(画像の解像度が28x28なため)となります。
- 次の層は`dropout`層です。
	- `50%`（0.5）のニューロンを**ランダムに無効化**します。これにより**過学習を防ぎます。**
- 次は`dense`層です。
	- ニューロン数（units）は64で、活性化関数はReLUです。
- 次も`dense`層です。
	- ニューロン数（units）は32で、活性化関数はReLUです。
- 最後は出力層`dense`です。
	- ニューロン数（units）は10で、これは１０種類のカテゴリを持つことを意味します。
		- 今回の場合だと、入力された画像から０、１、２、３、４、５、６、７、８、９のいずれかを推論します。
	- 活性化関数はSoftmaxです。これにより**最終的な確率分布を出力**します。
# モデルのコンパイル