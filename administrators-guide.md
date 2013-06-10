Apache Traffic Server™ はインターネットアクセスを加速させ、ウェブサイトのパフォーマンスを高め、かつて無いウェブホスティング性能を提供します。

この章は次のようなトピックについて書いてあります。

- [What Is Apache Traffic Server?](#apache-traffic-server-)
- [Traffic Server Deployment Options](#traffic-server-deployment-options)
  - [Traffic Server as a Web Proxy Cache](#traffic-server-as-a-web-proxy-cache)
  - [Traffic Server as a Reverse Proxy](#traffic-server-as-a-reverse-proxy)
  - [Traffic Server in a Cache Hierarchy](#traffic-server-in-a-cache-hierarchy)
- [Traffic Server Components](#traffic-server-components)
  - [The Traffic Server Cache](#the-traffic-server-cache)
  - [The RAM Cache](#the-ram-cache)
  - [The Host Database](#the-host-database)
  - [The DNS Resolver](#the-dns-resolver)
  - [Traffic Server Processes](#traffic-server-processes)
  - [Administration Tools](#administration-tools)
- [Traffic Analysis Options](#administration-tools)
- [Traffic Server Security Options](#traffic-server-security-options)

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

Traffic Server はウェブプロキシーキャッシュとして簡単に監視や設定ができるように構成されたいくつかコンポーネントによって構成されています。
これらのコンポーネントについて次に述べます。

## The Traffic Server Cache

Traffic Server キャッシュはオブジェクトストアと呼ばれるハイスピードオブジェクトデータベースによって構成されます。
オブジェクトは URL と関連するヘッダーに基づいたインデックスオブジェクトを保存します。
洗練されたオブジェクト管理により、オブジェクトストアは同じオブジェクトの（言語やエンコーディングタイプなどが）異なるバージョンをキャッシュすることができます。
これは無駄なスペースを最小化することによって、とても小さかったり、大きかったりするオブジェクトを効率的に保存することもできます。
キャッシュがいっぱいになった場合、Traffic Server は最もリクエストされるオブジェクトがすぐに利用可能で新しい状態であることを保証するために、古いデータを削除します。

Traffic Server はすべてのキャッシュディスクのあらゆるディスク不良を許容するようにデザインされています。
完全にディスクが壊れてしまった場合、Traffic Server はそのディスクを破損したと印をつけ、残りのディスクを使い続けます。
すべてのディスクが壊れた場合、Traffic Server は proxy-only モードに切り替わります。
特定のプロトコルやオリジンサーバーのデータを保存するための一定のディスクスペースを予約するためにキャッシュを分割することができます。
キャッシュに関するより詳しい情報は [Configuring the Cache]() を参照してください。

## The RAM Cache

Traffic Server はとても頻繁にアクセスされるオブジェクトを含む小さな RAM キャッシュを持っています。
特に一時的なトラフィックのピークの間に、このRAM キャッシュは最もポピュラーなオブジェクトを可能な限り速く提供し、ディスクからのロードを減らします。
この RAM キャッシュのサイズは必要な量に設定することができます。
より詳しい情報は [Changing the Size of the RAM Cache]() を参照してください。

## The Host Database

Traffic Server は Traffic Server がユーザーリクエストを満たすために接続するオリジンサーバーの ドメインネームサーバー(DNS) のエントリを保存するデーターベースをホストします。
この情報は将来のプロトコルインタラクションへの対応とパフォーマンスの最適化のために使われます。
加えて、ホストデータベースは次の情報を保存します。

- DNS 情報(ホストネームから IP アドレスを高速に引くため)
- 各ホストの HTTP バージョン(最新のプロトコルの機能はモダンなサーバーで使われているかもしれないため)
- 信頼性と可用性の情報(ユーザーが起動していないサーバーを待つことがないように)

## The DNS Resolver

Traffic Server はホスト名から IP アドレスへの変換を統合するために、高速で非同期な DNS リゾルバも含んでいます。
Traffic Server は遅くて月並みなリゾルバライブラリに渡すよりも、直接 DNS コマンドパケットを渡すことによって、DNS リゾルバをネイティブに実行します。
多くの DNS クエリが並列で渡され、高速な DNS キャッシュがポピュラーなバインディングをメモリに保存することにより、DNS トラフィックは減ります。

## Traffic Server Processes

Traffic Server はリクエストを返し、システムの状態を管理/制御/監視することを協調して動くための3つのプロセスを含んでいます。
この３つのプロセスは下に説明されています。

- `traffic_server` プロセスは Traffic Server のトランザクションプロセッシングエンジンです。
  コネクションをアクセプトしたり、プロトコルリクエストを処理したり、キャッシュやオリジンサーバーからドキュメントを提供することに責任を持ちます。

- `traffic_manager` プロセスは Traffic Server への命令と管理機能です。起動や監視と `traffic_server` プロセスを再設定したりすることに責任を持ちます。
  `traffic_manager` プロセスはプロキシオートコンフィギュレーションポートや統計のインターフェイスやクラスター管理とバーチャル IP フェイルオーバーについても責任を持ちます。

  `traffic_manager` プロセスが `traffic_server` プロセスが失敗していることを検知した場合、即座にプロセスを再起動するだけでなく、すべてのリクエストのコネクションキューをメンテナンスします。
  サーバーが完全に再起動する数秒前に到着したすべてのインカミングコネクションはコネクションキューに格納され、最初に来たものから順に処理されます。
  このコネクションキューはすべてのサーバーの再起動の際のダウンタイムからユーザーを守ります。

- `traffic_cop` プロセスは `traffic_server` と `traffic_manager` プロセスの両方の状態をモニターします。
  `traffic_cop` プロセスは定期的(毎分数回)に静的なウェブページを取得するハートビートリクエストを渡すことで `traffic_server` と `traffic_manager` に問い合わせます。
  失敗したとき(一定期間の間にレスポンスが帰って来ないときや不正なレスポンスを受け取ったとき), `traffic_cop` は `traffic_manager` と `traffic_server` プロセスを再起動します。

次の図は Traffic Server の3つのイラストです。

[http://trafficserver.apache.org/images/admin/process.jpg](http://trafficserver.apache.org/images/admin/process.jpg)

## Administration Tools

Traffic Server は次のような管理オプションを提供しています。

- Traffic Line コマンドラインインターフェイスはテキストベースのインターフェースです。Traffic Server のパフォーマンスとネットワークトラフィックを監視できます。
  また同じように、Traffic Server システムを設定することもできます。
  Traffic Line によって独立したコマンドや一連のコマンドのスクリプトをシェルで実行することができます。

- Traffic Shell コマンドラインインターフェイスは追加のコマンドラインツールで、Traffic Server システムを監視したり設定したりする独立したコマンドを実行することができます。
  Traffic Line や Traffic Shell を通じたどんな変更も自動的に設定ファイルを作ります。

- 最後に、多くのな言語から使うことのできるクリーンな C API があります。
  Traffic Server Admin Client は Perl でこのことを示しています。

# Traffic Analysis Options

Traffic Server はネットワークトラフィックの分析と監視のためのいくつかのオプションを提供しています。

- Traffic Line と Traffic Shell はネットワークトラフィック情報から入手した統計情報を集めて処理することを可能にします。

- トランザクションロギングは Traffic Server が処理したへすべてのリクエストとすべての検知したエラーの情報を (ログファイルの中に) 記録することを可能にします。
  ログファイルを分析することによって、どれほどのクライアントが Traffic Sever キャッシュを使用し、どれくらいの情報がリクエストされ、どのページがポピュラーなのかを確認することができます。
  特定のトランザクションがなぜエラーになり、 そのときの Traffic Server の状態がどうだったのかみることもできます。
  例えば、 Traffic Server が再起動したときや、クラスターコミュニケーションがタイムアウトしたときなどです。

　Traffic Server は Squid や Netscape などのいくつかの標準的なログフォーマットや固有のフォーマットをサポートしています。
  off-the-shelf 分析パッケージによって標準的なフォーマットのログを分析することができます。
  ログファイルの分析を助けるために、特定のプロトコルやホストの情報を含むようにログファイルを分割することができます。

トラフィック分析オプションは [Monitoring Traffic](http://trafficserver.apache.org/docs/trunk/admin/monitoring-traffic/) でより詳しく書かれています。

Traffic Server ロギングオプションは [Working with Log Files](http://trafficserver.apache.org/docs/trunk/admin/working-log-files/) に書かれています。

# Traffic Server Security Options

Traffic Server は Traffic Server システムと他のコンピュータネットワーク間のセキュアな通信を確立することを可能にする多数のオプションを提供しています。
セキュリティオプションを使うことによって、次のようなことができます。

- Traffic Server プロキシーキャッシュにアクセスするクライアントの管理
- あなたのサイトのセキュリティ設定にマッチする複数の DNS サーバーを使うための Traffic Server の管理
  例えば、Traffic Server はホストネームを解決する必要があるのがファイアーウォールの内側か外側かによって異なる DNS サーバーを使うことができます。
  これは透過的にインターネット上の外部サイトにアクセスすることを提供しつつ、インターナルネットワーク設定をセキュアに保つことを可能にします。
- クライアントが Traffic Server キャッシュからコンテンツにアクセスできるようになる前に、クライアントが認証されていることを検証する Traffic Server 設定
- SSL ターミネーションオプションを使うことによる、クライアントと Traffic Server 間のリバースプロキシーモードと Traffic Server とオリジンサーバー間の安全な接続
- SSL (Secure Socket Layer) によるアクセスの管理

Traffic Server セキュリティオプションは [Security Options](http://trafficserver.apache.org/docs/trunk/admin/security-options/) に詳しく述べられています。