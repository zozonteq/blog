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
1. [Godmode9](../DB/Software/Software_DATA/Godmode9.md)を起動する
2. 吸い出したいゲームカセットを刺す
3. `[C:] GAMECART`に移動する
4. `.trim.3ds`で終わるファイルを探し、`A`ボタンを押す
5. オプションが色々表示されるので`NCSD image options...`を選択
6. `Decrypt file (0:/gm9/out)`を選択することで暗号化の解除・吸い出しを同時進行できる。
7. しばらく待つと、`0:/gm9/out`配下にROMが吸い出される。

# 関連
[3DS更新データの吸い出し](3DS更新データの吸い出し.md)