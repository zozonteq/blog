---
date: 2024-12-11
aliases: HonoXでOpenAPIとSwaggerUIを導入する
tags: 
title: HonoXでOpenAPIとSwaggerUIを導入する
---
# はじめに

HonoではサードパーティミドルウェアとしてZod OpenAPIが提供されています。
https://hono.dev/examples/zod-openapi

これを使うことでOpenAPI仕様書が簡単に実装できるのですが、HonoのメタフレームワークであるHonoXではこの手段が使えません。
https://github.com/honojs/honox/issues/94

そのため、この記事ではHonoXでZod、OpenAPI、SwaggerUIを使う方法を紹介します。

# 使うパッケージ
```
bun add @hono/zod-openapi @hono/swagger-ui @asteasolutions/zod-to-openapi @hono/zod-validator
```
上は`Bun`での実行例で、今回使うパッケージをインストールします。軽くパッケージの説明を下に書きました。

`@hono/zod-openapi`:Honoで提供されていて、今回はOpenAPIに新しくルートを生やすために使います（`createRoute()`）

`@hono/swagger-ui`:これもHonoから提供されていて、Honoに`Swagger-UI`を実装するためのものです。OpenAPI仕様書を受け取り、それを元にドキュメントがGUIで見れるようになります。

`@asteasolutions/zod-to-openapi`: zodスキーマからOpenAPIへ変換するためのパッケージです。今回はレジストリの定義、パスの定義、OpenAPI仕様書の吐き出しをやってもらいます。

`@hono/zod-validator`:バリデータです。ユーザーからのリクエストの内容が正しいかどうかをチェックするために使います。

# どんな感じになる
例として、システム時間を返すAPIを実装しそれをOpenAPIに登録してみます。
```shell
app/routes/api
├── info.route.ts  // OpenAPIの登録、レスポンスやリクエストのzodスキーマを定義
└── info.ts        // 実際のAPIをここで実装
```
