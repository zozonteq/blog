---
tags:
  - "#3ds"
relation:
  - "[[blog/content/tips/3DS更新データの吸い出し.md|3DS更新データの吸い出し]]"
  - "[[blog/content/tips/3DSゲームのROM吸い出し.md|3DSゲームのROM吸い出し]]"
---
# 前提条件
- [Godmode9](../DB/Software/Software_DATA/Godmode9.md)導入済み
# 手順
1. START + POWERなどで[Godmode9](../DB/Software/Software_DATA/Godmode9.md)を起動
2. `[A:] /SYSNAND_SD/title/`に移動
3. `./0004000e`に対して`R + A`コンボから`Directory options`を立ち上げ`Search for titles`を選択しゲームタイトルのみを表示するようにする。
4. 一覧から欲しい更新データを探し、見つけたら`A`ボタンを押して`TMD file options...`を選択
5. `Build CIA(standard)`を選択しビルド終了まで待機
6. ビルド終了後、`0:/gm9/out`にビルド成果物が配置

# citraで更新データを適用するには
1. Citra上のメニューの「ファイル」から「CIAをインストール」を選択
2. ダイアログが表示されるので、吸い出した更新データ（`.cia`）を選択
3. 更新が適用される。

# 関連
- [3DSゲームのROM吸い出し](3DSゲームのROM吸い出し.md)