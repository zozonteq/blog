---
tags:
  - "#python"
---
# はじめに
[MacOS](../DB/Software/Software_DATA/MacOS.md)でPoetryを使ってTensorFlowの環境を構築する手順。
- Pythonのバージョン管理はpyenv
- その他仮想環境やパッケージなどはpoetryに任せる
# 前提条件
- [brew](../DB/Software/Software_DATA/brew.md)インストール済み
- pyenvインストール・構築済み
- アップルシリコン（M1,M2,M3,M4）
# 手順
## Poetryのインストール
```shell
brew install poetry
```
[brew](../DB/Software/Software_DATA/brew.md)を使ってpoetryをインストールします
## プロジェクトの作成
```shell
poetry new project_name
cd project_name
poetry env use 3.9
```
プロジェクトの作成から、プロジェクトで使うpythonバージョンの指定です。tensorflowのプロジェクトでは3.9が推奨されています。
- `poetry env use <python_version>`の実行の際、対象となるpythonのバージョンがすでにインストールされている必要があります
	- pyenvなどを用いることでインストールできます
	- `pyenv global 3.9`

poetryの設定で`poetry env use`が実行できない場合があります。その場合`pyproject.toml`の以下の項目を編集し、3.9以上3.10未満のpythonを使うように設定を更新します。
```toml
[tool.poetry.dependencies]
python = ">=3.9,<3.10"
```
## 仮想環境のアクティベート（有効化）
```
poetry shell
```
## パッケージのインストール
```shell
poetry add tensorflow-macos tensorflow-metal
```
[MacOS](../DB/Software/Software_DATA/MacOS.md)でApple Silicon(M1,M2,M3,M4)を使用しているので、上記のパッケージをインストールします。
## 実行
```
poetry run python main.py
```