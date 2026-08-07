# AE2 WCWT agent entrypoint

このファイルはCodex・LLM・自動レビューが、巨大なterminal screenとoptional integration群を毎回すべて読み込まずに作業範囲を決めるための入口です。

## 最小読込手順

1. 最初に本書と [`docs/CODEBASE_MAP.md`](docs/CODEBASE_MAP.md) だけを読む。
2. MapのTask routeを1つ選び、そのrouteに記載されたpackage・resource・直近testだけを開く。
3. `WirelessComprehensiveWorkTerminalScreen.java`等の大型fileは全文読込せず、対象panel/button/methodを検索して必要範囲だけ読む。
4. compile error、test failure、実依存関係が示した場合だけ隣接packageへ範囲を広げる。
5. 全client code、全panel、全optional compat、全resources、CHANGELOG全文の再帰読込を開始条件にしない。

## 固定契約

```text
Minecraft                 1.21.1
Loader                    NeoForge
Applied Energistics 2     19.2.17
Primary feature           Wireless Comprehensive Work Terminal
Current version           1.1.0
```

Optional integrationsは、対象MODが存在する場合だけbutton、panel、handlerを有効化します。対象MODがない環境でclass loading、static initialization、menu registrationを失敗させません。

Main ME storage、crafting、pattern encoding、server inventory mutationはAE2/server authorityを維持します。client UIだけでinventory、pattern、magnet、trash、restock stateを確定しません。

`build.gradle`に残るlocal `compileOnly files(...)` development pathは開発環境依存です。存在を前提に設計を変えず、dependency修正では公開可能なcoordinate/optional boundaryを確認します。

## 安全規則

- menu/data slot/network packetのclient/server契約を片側だけ変更しない。
- optional integrationはmod presenceとsideを確認してからclassへ触れる。
- crafting grid lock、pattern mode、multiplier、temporary cacheの既存state遷移を維持する。
- hotkeyはscreen focus、menu ownership、server validationを無視しない。
- mixin target/versionが不一致なら推測で適用しない。
- build成功だけで全panel、JEI/EMI、wireless、multiplayerを検証済みと書かない。

## 編集規則

- UI変更では対象panel/classと関連menu/network処理だけを先に読む。
- optional integration変更では`compat`内の対象MODだけをscopeにする。
- entrypoint、主要package、大型screen分割、重要resourceの位置が変わる場合は `docs/CODEBASE_MAP.md` を更新する。
- 300KB級main screen、large coding panel、CHANGELOGは対象symbol/heading周辺だけを読む。

## 検証順

```text
対象compile/test task
-> ./gradlew clean build --no-daemon
-> 必要な場合だけNeoForge実環境でscreen / menu / packet / optional mod確認
```

CIやcompileだけの結果を全UI・multiplayer互換済みとして扱いません。
