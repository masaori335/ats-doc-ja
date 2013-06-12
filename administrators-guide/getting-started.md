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
