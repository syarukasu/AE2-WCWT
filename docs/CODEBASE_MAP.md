# AE2 WCWT codebase map

> **Navigation only.** このMapはCodex・LLM・reviewerの探索量を減らすためのindexです。機能契約はREADME、source、現行Issueを使用します。

## 使い方

1. [`../AGENTS.md`](../AGENTS.md)を読む。
2. 下のTask routeを1つ選ぶ。
3. `Source scope`だけを開き、button/panel/menu/packet symbol検索から始める。
4. compile/test結果が別package依存を示した場合だけscopeを広げる。

初期読込の対象外:

```text
build/**
.gradle/**
生成JAR / run directory / logs
CHANGELOG.md全文
全client package
全optional compat
全assets/lang
```

## 固定座標

```text
Minecraft        1.21.1
Loader           NeoForge
AE2              19.2.17
Version          1.1.0
```

## Task router

| Route | Task | Read first | Source scope | Verification scope |
| --- | --- | --- | --- | --- |
| `C0` | 機能、dependency、optional integration、文書 | `../README.md`, `build.gradle`, `dependencies.gradle` | docs/build metadata中心 | dependency resolution、`build` |
| `U1` | Main terminal layout、storage view、buttons、render/input | READMEのMain Terminal UI | `client/WirelessComprehensiveWorkTerminalScreen.java`の対象symbol、必要な`client/gui`だけ | client compile、manual screen smoke |
| `P1` | Extended UI panels、coding panel、per-panel controls | READMEのpanel節 | `client/gui/panels`の対象panel、関連API/helper | panel-specific manual test |
| `K1` | Pattern encoding/cache、crafting/processing/smithing/stonecutting、multiplier | READMEのPattern sections | menu/item/apiのpattern/cache関連、対象client panel | encode/cache/reopen test |
| `M1` | Menu、data sync、server action、packet | READMEのbehavior、対象UI route | `menu`, network関連package、`WcwtMenuEvents.java` | client/server packet and menu test |
| `W1` | Wireless settings、magnet、restock、pickup、trash | READMEのTop-right/Settings sections | `WcwtWirelessFeatureEvents.java`, item/menu/client stateの対象だけ | singleplayer + dedicated server smoke |
| `I1` | Optional MOD integration | READMEのOptional integrations | `compat`内の対象MOD subpackageだけ | absent/present両構成のload test |
| `H1` | Hotkeys、keybindings、focus/input arbitration | READMEのhotkey説明 | `hotkeys`, client keybinding、対象panel | key conflict/focus/manual test |
| `X1` | Mixin、host API、AE2 screen/menu extension | source descriptor、対象Issue | `mixin`, `api`の対象interfaceとtarget classだけ | Mixin apply、runtime target test |
| `R1` | Item/menu registration、metadata、models/lang | READMEのitems/features | `init`, `item`, 対象resources namespaceだけ | resource validation、game load |
| `V1` | Build、local compileOnly paths、release | `build.gradle`, `dependencies.gradle`, `gradle.properties` | build files、workflow、該当test | `clean build` |

## Package map

| Package/path | Responsibility |
| --- | --- |
| `WcwtMod.java` | NeoForge mod entrypointとregistration/bootstrap |
| `WcwtMenuEvents.java` | menu lifecycle/event entry |
| `WcwtWirelessFeatureEvents.java` | wireless-related common events |
| `client` | main screen、client state、keybindings、tooltips |
| `client/gui` | reusable rendering/control helpers |
| `client/gui/panels` | extended UI panelsとadvanced coding UI |
| `menu` | server-authoritative menu/container behaviorとdata sync |
| `item` | terminal item、cards、interaction behavior |
| `api` | host interfaces for extended UI/cache/crafting lock |
| `compat` | optional mod integrations、presence-gated bridge |
| `hotkeys` | panel/action hotkeysとdispatch |
| `config` | client/common configuration |
| `command` | diagnostic/admin commands |
| `helpers` | shared small utilities |
| `init` | item/menu/creative tab/network registration |
| `mixin` | AE2/optional mod target hooks |
| `src/main/resources` | NeoForge metadata、Mixin descriptors、assets/lang/models |

## 主要entrypointとhot files

| Purpose | Path |
| --- | --- |
| Mod entrypoint | `src/main/java/com/lhy/wcwt/WcwtMod.java` |
| Menu events | `src/main/java/com/lhy/wcwt/WcwtMenuEvents.java` |
| Wireless events | `src/main/java/com/lhy/wcwt/WcwtWirelessFeatureEvents.java` |
| Main terminal screen | `src/main/java/com/lhy/wcwt/client/WirelessComprehensiveWorkTerminalScreen.java` |
| Advanced coding panel | `src/main/java/com/lhy/wcwt/client/gui/panels/AdvancedCodingPanel.java` |
| Client setup | `src/main/java/com/lhy/wcwt/client/ModClientSetup.java` |
| Favorites state | `src/main/java/com/lhy/wcwt/client/WcwtFavorites.java` |
| Keybindings | `src/main/java/com/lhy/wcwt/client/WcwtKeybindings.java` |
| Public host interfaces | `src/main/java/com/lhy/wcwt/api` |
| Optional integrations | `src/main/java/com/lhy/wcwt/compat` |
| Build dependency map | `dependencies.gradle` |

`WirelessComprehensiveWorkTerminalScreen.java`は約300KB、`AdvancedCodingPanel.java`も大型です。全文読込は禁止し、対象button/panel/method名で検索して必要範囲だけ読む。

## Optional integration rule

対象MODごとに次の順で確認する。

```text
README上のoptional status
-> dependencies.gradle/build.gradle上のcompile boundary
-> compat subpackage
-> registration/presence gate
-> client/server side
-> absent環境のload
-> present環境のfeature
```

複数optional modを同時に読むのは、競合が再現した場合だけにする。

## 文書の読み分け

| Need | Document |
| --- | --- |
| feature、UI layout、requirements | `../README.md` |
| build dependency versions | `../dependencies.gradle`, `../build.gradle` |
| version history | `../CHANGELOG.md`の対象versionだけ |
| Gradle/mod coordinates | `../gradle.properties` |

## 最小検証コマンド

```text
./gradlew clean build --no-daemon
```

UI変更は実client、menu/network変更はdedicated serverを含む両側、optional integration変更はMOD absent/presentの両構成で確認する。未実施項目を検証済みと書かない。

## 省トークン用prompt

```text
AGENTS.mdとdocs/CODEBASE_MAP.mdの<Route ID>だけを基準に作業する。
Task: <作業内容>
最初はroute記載のpackageと対象symbol以外を読まない。
大型screen/panelは全文読込しない。
別scopeへ広げる場合はcompile dependencyまたは再現結果を根拠として示す。
```
