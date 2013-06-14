- [Before you start]()
- [Building Traffic Server]()
- [Start Traffic Server]()
- [Start Traffic Line]()
- [Stop Traffic Server]()

# Before you start

Traffic Server を始める前に、どのバージョンを使いたいか決める必要があります。
Traffic Server は Apache [apr]() や [httpd]() が行っているように "安定性"を示すために、"セマンティック バージョニング" を使用しています。

つまり、バージョンは次のような三つ組みのバージョンで構成されています。 MAJOR.MINOR.PATCH

最も重要なことは MINOR が偶数のもの ( 3.0.3 や 3.2.5 のような) はプロダクション安定リリースで、MINOR が奇数のものは開発者向けであることを示していることを知っていることです。

トランク、マスター、もしくは実際のリリースについて話しているとき、"-unstabe" や "-dev" といった言い方をします。
トランク、マスター、TIP、HEAD のような Version Control System 内の最新のコードの呼び方はすべて交換可能です。
しかし、"-dev" (もしくは 不運なことに "-unstable" と名前づけられたもの) は次のことがわかります。
"3.3.1-dev"のような特定のリリースはプロダクションレディーなものとみなせる程の十分なテストを受けていません。

お使いのディストリビューションが Traffic Server をあらかじめパッケージされていない場合、 [downloads]() へ行き、最も適切だと考えられるバージョンを選んでください。
最先端のものが本当に欲しい場合は、[git repository]() からクローンすることができます。

Pull-Request を送ることができる [Github Mirror]() を持っていますが、これはこれは最新版ではないかもしれないことに注意してください。

# Building Traffic Server

Traffic Server をソースコードからビルドするために、次の(開発)パッケージが必要です。

- pkgconfig
- libtool
- gcc (>= 4.3 or clang > 3.0)
- make (GNU Make!)
- openssl
- tcl
- expat
- pcre
- pcre
- libcap
- flex (for TPROXY)
- hwloc
- lua

git クローンからビルドする場合は、次のものも必要です。

- git
- autoconf
- automake

git からビルドする例をお見せしましょう。

```
git clone https://git-wip-us.apache.org/repos/asf/trafficserver.git
```

次に、`trafficserver` に `cd` してコマンドを実行します。

```
autoreconf -if
```

これは `configure` ファイルを `configure.ac` から生成するので、次のコマンドを実行できます。

```
./configure --prefix=/opt/ats
```

デフォルトでは Traffic Server ユーザーとして `nobody` ユーザーを使用することに注意してください。プライマリーグループについても同様です。
これを変更したい場合、上書きすることができます。

```
./configure --prefix=/opt/ats --with-user=tserver
```

標準的なパス ( /usr/local や /usr ) に依存関係がない場合、パスオプションを設定する必要があります。

```
./configure --prefix=/opt/ats --with-user=tserver --with-lua=/opt/csw
```

ほとんどのパスオプションは "INCLUDE_PATH:LIBRARY_PATH" というフォーマットを受け入れます。

```
./configure --prefix=/opt/ats --with-user=tserver --with-lua=/opt/csw \
    --with-pcre=/opt/csw/include:/opt/csw/lib/amd64
```

プロジェクトをビルドするために `make` コマンドを実行しましょう。
ビルドの一般的な正常さを確かめるために `make check` コマンドを実行することを強く推奨します。

```
make
make check
```

最後に `make install` コマンドを実行しましょう。(おそらく root になることが必要でしょう)

```
sudo make install
```

レグレッションテストを実行することも推奨します。
これはデフォルトレイアウトで正常に動作することに注意してください。

```
cd /opt/ats
sudo bin/traffic_server -R 1
```

Traffic Server をシステム上にインストースした後、次のどれでもできます。
