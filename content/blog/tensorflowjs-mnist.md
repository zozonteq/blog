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
先日、ensorflow.jsを使って手書きの数字の分類モデルを作成したのでその時のメモです！

mnistはtensorflow（Pythonの方）の公式チュートリアル序盤に登場するくらい有名なデータセットで、機械学習初学者にもおすすめです。
## この記事で取り扱うこと
- JavaScript（Node.JS）で機械学習モデルの構築
- parquetファイルの読み取り

ニューラルネットや機械学習の細かい仕組みについてはあまり触れられないかもしれません。
## 環境

| 名前      | 値                |
| ------- | ---------------- |
| JSランタイム | node.js v20.18.0 |
| OS      | macOS 15.1       |

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
- ロードしたデータは