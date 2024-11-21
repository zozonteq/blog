---
tags:
  - discord-bot
title: モジュール式ディスコボット開発テンプレート
date: 2024-09-23T00:00:00.000+09:00
---
モジュール性を重視したTypeScript製の[DIscord](../DB/Software/Software_DATA/DIscord.md)ボット開発テンプレート。

# 特長
- モジュール式の開発により、機能の追加や管理が可能
	- 特に多機能Botなどの開発に向いています
- TypeScriptにより型安全
- ライブラリに[Discordeno](https://github.com/discordeno/discordeno)を使用
	- モジュールシステムによりイベントリスナーを複数設置可能
- ランタイムはDenoをサポート
	- セキュリティと保守性を担保

# ダウンロード先
https://github.com/zozonteq/mobulable-discord-bot-template
# 開発スタイル
基本的に何か機能を付け足す場合はモジュールを新たに作成し、そこの機能を記述してく形になります。このような開発スタイルから、多機能Botの開発には特に向いています。
## モジュールの作成
モジュールを作成するには、`src/modules`の下に新たにTypeScriptファイルを作成し以下のように記述します。
```typescript
import { BotModule } from "../module_system/bot_module.ts";
import { createEventHandlers} from "../deps.ts"
import { logger } from "../logger.ts";

export const exampleModule = new BotModule(
  {
    name: "Example Module",
    description: "ここに説明",
    handlers:createEventHandlers({
      ready: (_,payload) => {
        logger.info(`Hello, World!`)
      }
    })
  }
)
```
- BotModuleクラスをインスタンス化することでモジュールを作成できます。
- オプションの説明
	- `name:string`
		- モジュールの名前です
	- `description : string`
		- モジュールの説明をここに記述します
	- `handlers`
		- イベントハンドラの登録です
		- `createEventHandlers()`を呼び出し、引数にイベントを記述します
		- この例では`ready`イベントが呼ばれた際に`Hello, World!`と出力するといったプログラムになります。
		- 他にもメッセージを受け取った時（`createMessage`）などのイベントが使えます。詳細については[discordenoのドキュメント](https://deno.land/x/discordeno@18.0.1/mod.ts)をご覧ください
## モジュールのロード
モジュールを開発したら次はそれを読み込ませます
`src/modules.ts`を開いて、`new BotModuleManager({...})`の引数に先ほどの`exampleModule`のインスタンスを追加します。

```typescript
import { BotModuleManager } from "./module_system/bot_module_manager.ts";
import { inviteLinkModule } from "./modules/invite-link.ts";
import { readyMessageModule } from "./modules/ready.ts";
import { exampleModule } from "./modules/example-module.ts"; // ++ 追加 

export const moduleManager = new BotModuleManager(
    [
        readyMessageModule          .Enable(),
        inviteLinkModule            .Enable(),
		exampleModule               .Enable() // ++ 追加
    ]
);
```

- モジュールの内容を記述した`.ts`ファイルのインポートを忘れないでください。
これで、`exampleModule`が読み込まれるようになりました
## 起動
```shell
deno run dev
```
上記のコマンドでbotが開始されます。ログが流れるので、そこに「`Hello,World!`」と出力されれば成功です。