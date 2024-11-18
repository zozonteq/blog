> [!NOTE]
> 執筆途中です。
# はじめに
新しくM3のMacbook Airを購入したので、それに伴い環境を再構築しました。
## Cask
[DIscord](../DB/Software/Software_DATA/DIscord.md)や[Firefox](../DB/Software/Software_DATA/Firefox.md)といったGUIアプリケーションも[brew](../DB/Software/Software_DATA/brew.md)の`--cask`オプションを使ってインストール・管理することができるので、可能な限り[brew](../DB/Software/Software_DATA/brew.md)経由でインストールする方針で構築しました。

また[brew](../DB/Software/Software_DATA/brew.md)を使う利点として`Brewfile`により移行が楽になるという利点があります。詳細については下記の記事がわかりやすいと思うので、興味があればご覧ください。  
参考： https://zenn.dev/usagiga/articles/migrate-using-brew-bundle

# [Firefox](../DB/Software/Software_DATA/Firefox.md)
ブラウザには[Firefox](../DB/Software/Software_DATA/Firefox.md)を採用しました。以前までは広告ブロックが目的で[Brave](../DB/Software/Software_DATA/Brave.md)を使っていましたが、拡張機能で広告ブロッカーをインストールすれば[Firefox](../DB/Software/Software_DATA/Firefox.md)で事足ります。
他にも以下のような利点があります

- Rustで開発されているので高速で安全
- オープンソース

使っている拡張機能に関しては下記の記事をご覧いただければなと思います。（書きかけですが）
- [自分がFirefoxで使っている拡張機能](自分がFirefoxで使っている拡張機能.md)
# [Amethyst](../DB/Software/Software_DATA/Amethyst.md)
タイリングウィンドウマネージャです。Linuxで使われる[xmonad](../DB/Software/Software_DATA/xmonad.md)に影響を受けています。
ウィンドウを自分で配置する手間が省けるので非常に便利です。 
# [Raycastr](../DB/Software/Software_DATA/Raycastr.md)
![](../attachments/Pasted%20image%2020241118115212.png)
強力なランチャーアプリです。ランチャーアプリというのはMacの標準のSpotlightみたいなものです。
CMD + SpaceでRaycastrが開き、このようにアプリケーションをすぐに立ち上げることができます。
Raycastrは多機能で以下のようなことができます
- 簡易的な計算
	- 検索窓に`2 ** 8`などの計算式を入れることで計算結果を出力してくれます。`sin`や`log`などの関数もサポートされています。
- アプリケーションの連携
- スニペット
参考 https://zenn.dev/fumi_sagawa/articles/2ff5fd9c03fbcd

# [Obsidian](../DB/Software/Software_DATA/Obsidian.md)
ナレッジベースのノートアプリです。ローカル環境の[Notion](../DB/Software/Software_DATA/Notion.md)みたいなもんです。
このブログも[Obsidian](../DB/Software/Software_DATA/Obsidian.md)で書いています。コミュニティのプラグインが豊富だったり、ノート同士のつながりを可視化することができ、便利です。
# コマンドライン環境（CLI）
ここからはCLI環境についてです。
# [iTerm2](../DB/Software/Software_DATA/iTerm2.md)
ターミナルは一番人気な[iTerm2](../DB/Software/Software_DATA/iTerm2.md)にしました。
# [zsh](../DB/Software/Software_DATA/zsh.md)
[nushell](../DB/Software/Software_DATA/nushell.md)や[fish](../DB/Software/Software_DATA/fish.md)などとも迷いましたが、[zsh](../DB/Software/Software_DATA/zsh.md)はmacOS標準なのでzshにしました。
## その他コマンド
- bat
- ripgrep
- eza
- peco
- fzf