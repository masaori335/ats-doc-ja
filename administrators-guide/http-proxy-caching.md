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

# Ensureing Cached Object Freshness

Traffic Server がウェブオブジェクトへのリクエストを受け取った際、最初にリクエストされたオブジェクトをキャッシュに入れようとします。
オブジェクトがキャッシュにある場合、Traffic Server はオブジェクトが提供するのに十分新しいかどうかを確認します。
Traffic Server は HTTP オブジェクトに作成者が指定した有効期限をサポートしています。
Traffic Server はこれらの有効期限を固く守ります。つまり、どれだけ頻繁にオブジェクトが変更されるかと、管理者が選んだフレッシュネスガイドラインに基づいて、有効期限を選択します。
オブジェクトはまた、依然として新しいかどうかをオリジンサーバーへ見に行くことにより、再確認されます。

## HTTP Object Freshness

Traffic Server はキャッシュした HTTP オブジェクトが新しいかどうかを次のことによって決定します。

- Checking the `Expires` or `max-age` header

  いくつかの HTTP オブジェクトは `Expire` ヘッダーや `max-age` ヘッダーを含んでいます。これらはオブジェクトがどれくらいの期間キャッシュできるかどうかを明確に定義しています。
  Traffic Server はオブジェクトがフレッシュであるかどうかを決定するために、現在時刻と有効期限を比較します。

- Checking the `Last-Modified` / `Date` header

  HTTP オブジェクトが `Expire` ヘッダーや `max-age` ヘッダーを持っていない場合、Traffic Server はフレッシュネスリミットを次の式で計算します。

```
freshness_limit = ( date - last_modified ) * 0.10
```

  この `date` はオブジェクトのサーバーのレスポンスヘッダーの日付で、`last_modified` は `Last-Modified` ヘッダーの日付です。
  `Last-Modified` ヘッダーが無い場合、Traffic Server はオブジェクトがキャッシュに書かれた日時を使用します。
  `0.10` (10 %) という値は必要に応じて、増減することができます。( [Modifying the Aging Factor for Freshness Computations] を参照してください。)

  計算されたフレッシュネスリミットは最小値と最大値に紐づけられます。詳細は [Setting an Absolute Freshness Limit] を参照してください。

- Checking the absolute freshness limit

  `Expires` ヘッダー等を持っていない、もしくは `Last-Modified` と `Date` ヘッダーの両方をもっていないHTTP オブジェクトについて、
  Traffic Server は最小で最大のフレッシュネスリミットを使用します。[Setting an Absolute Freshness Limit] を参照してください。

- Chacking revalidate rule in the `cache.config` file

  再確認ルールは特定の HTTP オブジェクトにフレッシュネスリミットを適用します。
  特定のドメインや IP アドレスから来たオブジェクト、特定の正規表現を含む URL を持つオブジェクトや特定のクライアントからリクエストされたオブジェクトなどにフレッシュネスリミットを設定することができます。

## Modifiying Aging Factor for Freshness Computations

オブジェクトが有効期限に関する情報を持っていない場合、Traffic Server は `Last-Modified` と `Date` ヘッダーからフレッシュネスを見積もります。
デフォルトでは Traffic Server は最後に更新されてからの経過時間の 10 % キャッシュします。
必要に応じて、増減することができます。

フレッシュネスの計算のための期間の要素を変更するためには、

1. `record.config` の次の値の変更
  - `proxy.config.http.cache.heuristic_lm_factor`
2. 設定の変更を適用するための `traffic_line -x` コマンドの実行

## Setting absolute Freshness Limits

いくつかのオブジェクトは `Expires` ヘッダーを持っていない、もしくは `Last-Modified` と `Date` ヘッダーの両方を持っていないことがあります。
これらのオブジェクトがキャッシュされてどの程度フレッシュであると考えられるか制御するために、 *absolute freshness limit* があります。

フレッシュネスリミットの絶対値を明確にするために

1. `record.config` の次の値を変更してください。

  - `proxy.config.http.cache.heuristic_min_lifetime`
  - `proxy.config.http.cache.heuristic_max_lifetime`

2. 設定の変更を適用するために `traffic_line -x` コマンドの実行してください。

## Specifying Header Requirements
