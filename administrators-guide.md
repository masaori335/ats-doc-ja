Apache Traffic Server™ はインターネットアクセスを加速させ、ウェブサイトのパフォーマンスを高め、かつて無いウェブホスティング性能を提供します。

この章は次のようなトピックについて書いてあります。

- [What Is Apache Traffic Server?](#apache-traffic-server-)
- Traffic Server Deployment Options
  - Traffic Server as a Web Proxy Cache
  - Traffic Server as a Reverse Proxy
  - Traffic Server in a Cache Hierarchy
- Traffic Server Components
  - The Traffic Server Cache
  - The RAM Cache
  - The Host Database
  - The DNS Resolver
  - Traffic Server Processes
  - Administration Tools
- Traffic Analysis Options
- Traffic Server Security Options

# Apache Traffic Server とは？

グローバルデータネットワークは毎日の一部になっています。
つまりインターネットユーザーは日常生活の基盤の上で数１０億ものドキュメントやテラバイトのデータを世界の隅から隅へリクエストします。
情報は無料で豊富でアクセス可能です。
不幸なことに、グローバルデータネットワーキングは過負荷なサーバーや混雑したネットワークと格闘している IT 専門家にとっては悪夢です。
増え続けるデータ需要を絶えず、期待通りに動くように対応することはチャレンジングなことです。

Traffic Server は高性能なウェブプロキシーキャッシュで、ネットワーク効率とパフォーマンスを頻繁にアクセスされる情報をネットワークの端でキャッシュすることで改善します。
これはエンドユーザーに物理的に近いコンテンツを届ける一方で配信と帯域使用量を減らすことを可能とします。

Traffic Server は商用のコンテンツ配信やインターネットサービスプロバイダー( ISP )やバックボーンプロバイダーや巨大なイントラネットを現行の利用可能な帯域を最大化することで改善するようにデザインされています。

# Traffic Server Deployment Options

あなたの需要にもっと合うように、Traffic Server はいくつかの方法で配置することができます。

- ウェブプロキシーキャッシュ
- リバースプロキシー
- キャッシュヒエラルキーの一部

次のセクションはこれらの Traffic Server のデプロイメントオプションのサマリーを提供します。
これらのすべてのプションで Traffic Server は `シングルインスタンス` か `マルチノードクラスター` として動作することを覚えておいて下さい。

## Traffic Server as a Web Proxy Cache

ウェブプロキシーキャッシュとして、Traffic Server はウェブコンテンツのユーザーリクエストを受け取り、宛先のウェブサーバー(オリジンサーバー)へ届けます。
リクエストこんテンスがキャッシュから使えない場合、Traffic Server はプロキシーとして振る舞います。
つまり、コンテンツをユーザーに代わってコンテンツを取得し、また将来のリクエストを満たすためにコピーを保持します。　
Traffic Server ははっきりとしたプロキシーキャッシュを提供します。ユーザーのクライアントソフトウェアはTraffic Server に直接リクエストを送るように設定されていなければなりません。
Explicit プロキシーキャッシュについては [Explicit Proxy Caching] の章で述べています。

## Traffic Server as a Reverse Proxy

リバースプロキシーとして、Traffc Server はユーザーが接続しようとするオリジンサーバーとして設定されています。
(一般的に、オリジンサーバーの宣伝されたホスト名は Traffic Server に解決され、実際のオリジンサーバーのように振る舞います。)
リバースプロキシー機能はサーバーアクセラレーションとも呼ばれます。
リバースプロキシーは [Reverse Proxy and HTTP Redirects] で詳しく述べられています。

## Traffic Server in a Cache Hierarchy

Traffic Server は柔軟にキャッシュヒエラルキーに参加することができます。
その中で 一つのキャッシュからは満たされないインターネットリクエストは他のコンテンツを支配していて、近いキャッシュに近接している局部的なキャッシュへ送られます。
プロキシーサーバーの階層の中で Traffic Server は他の Traffic Server システムや似たキャッシングプロダクトの親や子として振る舞います。

Traffic Server は ICP(Internet Cache Protocol) のピアイングをサポートしています。
階層的キャッシュは [Hierachical Caching] で詳しく述べられています。

# Traffic Server Components

## The Traffic Server Cache
## The RAM Cache
## The Host Database
## The DNS Resolver
## Traffic Server Processes
## Administration Tools

# Traffic Analysis Options

# Traffic Server Security Options
