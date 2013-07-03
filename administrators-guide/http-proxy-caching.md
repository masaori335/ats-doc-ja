Web proxy caching はよくアクセスされるウェブオブジェクト(書類、画像、記事など)のコピーを保存し、ユーザーの要求に対してこの情報を供給することを可能にします。
これはパフォーマンスを改善し、インターネットの帯域をほかのタスクのために空けます。

この章では次のようなトピックについて書いています。

- Understanding HTTP Web Proxy Caching
- Ensuring Cached Object Freshness
  - HTTP Object Freshness
  - Modifying Aging Factor for Freshness Computations
  - Setting absolute Freshness Limits
  - Specifying Header Requirements
  - Cache-Control Headers
  - Revalidating HTTP Objects
- Scheduling Updates to Local Cache Content
  - Configuring the Scheduled Update Option
  - Forcing an Immediate Update
- Pushing Content into the Cache
  - Configuring Traffic Server for PUSH Requests
  - Understanding HTTP PUSH
  - Tools that will help manage pushing
- Pinning Content in the Cache
- To Cache or Not to Cache?
- Caching HTTP Objects
- Client Directives
  - Configuring Traffic Server to Ignore Client no-cache Headers
    - Origin Server Directives
  - Configuring Traffic Server to Ignore Server no-cache Headers
    - Configuring Traffic Server to Ignore WWW-Authenticate Headers
    - Configuration Directives
  - Disabling HTTP Object Caching
    - Caching Dynamic Content
    - Caching Cookied Objects
- Forcing Object Caching
- Caching HTTP Alternates
  - Configuring How Traffic Server Caches Alternates
  - Limiting the Number of Alternates for an Object
- Using Congestion Control

# Understanding HTTP Web Proxy Caching

インターネットユーザはインターネット上のウェブサーバーへリクエストを出します。
キャッシュサーバーはこれらのリクエストを満たすために *ウェブプロキシサーバー* として振る舞わなくてはなりません。
ウェブプロキシーサーバーがウェブオブジェクトへのリクエストを受け取った後は、そのリクエストを返すか、*オリジンサーバー* (リクエストされた情報のオリジナルコピーを持っているウェブサーバー)へ転送します。
Traffic Server proxy は *explicit proxy caching* をサポートしています。
この際、 ユーザーのクライアントソフトが Traffic Server proxy へ直接リクエストを送るように設定されている必要があります。
次のオーバービューは Traffic Server がどのようにリクエストを返すかを描いています。

1. Traffic Server がウェブオブジェクトへのクライアントリクエストを受け取ります。
2. オブジェクトのアドレスを用いて、Traffic Server はオブジェクトデータベース( *キャッシュ* ) にリクエストされたオブジェクトを探します。
3. キャッシュにオブジェクトがある場合、Traffic Server はオブジェクトが提供するのに十分新しいか確認します。
   新しい場合、Traffic Server は *キャッシュヒット* (後述) としてクライアントにそれを提供します。
4. キャッシュのデータが古い場合、Traffic Server はオリジンサーバーへ接続し、オブジェクトが依然新しいかどうか確認します。(再確認)
   新しい場合、Traffic Server はすぐにキャッシュしているコピーをクライアントに送ります。
5. オブジェクトがキャッシュに無い場合(*キャッシュミス*) やサーバーがキャッシュしたコピーをもはや有効ではないと判断した場合、
   Traffic Server はオリジンサーバーからオブジェクトを取得します。
   オベジェクトは一度クライアントに流され、 Traffic Server キャッシュに配置します。(下の図を見てください)

一般的にキャッシュは前述の概要で説明したものよりも複雑です。
詳しく述べると、概要では Traffic Server がどのように新しさを保証し、正しい HTTP alternates を提供し、キャッシュできない/するべきではないオブジェクトへのリクエストを扱うかについて説明されていませんでした。
次の章はこれらのことについてとても詳しく説明します。