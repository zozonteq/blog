---
tags:
  - "#3ds"
---
# 前提条件
- Godmode9導入済み
# 手順
1. START + POWERなどでGodmode9を起動
2. `[A:] /SYSNAND_SD/title/`に移動
3. `./0004000e`に対してR + Aコンボから`Directory options`を立ち上げ`Search for titles`を選択しゲームタイトルのみを表示するようにする。
4. 一覧から欲しい更新データを探し、見つけたら`A`ボタンを押して`TMD file options...`を選択
5. `Build CIA(standard)`を選択しビルド終了まで待機
6. ビルド終了後、`0:/gm9/out`にビルド成果物が配置

# citraで更新データを適用するには
1. Citra上のメニューの「ファイル」から「CIAをインストール」を選択
2. ダイアログが表示されるので、吸い出した更新データ（`.cia`）を選択
3. 更新が適用される。