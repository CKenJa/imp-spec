# IMP 標準コア(IMP-Core)

以下はデータパック・モジュールがIMPに準拠していると言える最低限の基準である。

- [1. バニラには手を付けない](#1-バニラには手を付けない)
- [2. 全てに名前空間を](#2-全てに名前空間を)
  - [2a. エンティティタグの名前空間](#2a-エンティティタグの名前空間)
  - [2b. カスタムアイテムタグの名前空間](#2b-カスタムアイテムタグの名前空間)
  - [2c. スコアボードのオブジェクトの名前空間](#2c-スコアボードオブジェクトの名前空間)
  - [2d. storageの名前空間](#2d-storageの名前空間)

## 1. バニラには手を付けない

定義:

- 出来る限りバニラの「`minecraft`」名前空間下のものを変更しないこと。

根拠:

- バニラの要素上で競合が起こることを回避できる
- 将来追加される機能を意図せず使用/上書きするリスクを回避できる

警告:

- バニラのルートテーブルなどタグなどと違って機能拡張できず、上書きしかできない。どうしても上書きしたい場合は明確に文章化している限り許容される。
- 関数/ブロックにバニラタグをつけるのは問題ない。ただし(`"replace": true`などで)上書きするのはほとんどの場合避けるべき。

推奨事項:

- バニラデータを上書きする必要がある場合(ルートテーブルなど)は、変更を出来るだけ明確に抽象化するように。
  - ルートテーブルの場合の例：バニラルートテーブルにカスタムアイテムのNBT等を直接書き込む代わりに、名前空間下にカスタム戦利品テーブルを作り、バニラルートテーブルから参照する。これにより将来の変更が容易になり、他のパックが干渉しやすくなる。
- 全体として他のデータパックもバニラ名前空間と同じように扱ってかうように。他のパック制作者に礼儀正しく、必ずしも自分のパックだけが実行されているとは限らないと考えるように。

## 2. 全てに名前空間を

### 2a. エンティティタグの名前空間

定義:

- 全てのエンティティタグにはモジュール名前空間の接頭辞と区切りが付けられる。
- (ノート: エンティティタグ `tag`コマンドで扱う、`Tags`NBT内に保存されるタグです)

根拠:

- 他の準拠モジュールのエンティティタグが競合する効果的に回避できる。
- 規格に準拠しないパックのエンティティタグが競合する可能性を大幅に下げられる。

注意事項:

- 文字「:」は名前空間の区切り字として使用することはできません。[未翻訳]

推奨事項:

- 名前空間と異なる階層のタグの両方の区切り文字として「`.`」を使い、単語の区切り字として「`_`」を使う(snake_case)。
- タグは冗長であること。最適化のためにプロジェクトの可読性を犠牲にしない。もしパフォーマンスが気になるなら、最適化ソフトを使い、最適化したものをエンドユーザー配布してください。
(エンドユーザー: データパックを実際に使ったり遊んだりする人のこと)

例:

- 悪: `PlaytrackerIgnore`
- 悪: `playtracker_ignore`
- 正: `playtracker.ignore`
- 正: `playtracker.talked_to_villager`
- 正: `playtracker.sneaking.stopped`

### 2b. カスタムアイテムタグの名前空間

意味:

- すべてのカスタムアイテムタグフあ、モジュールにちなんで名づけられた専用NBT compoundの中に含まれる。
- (訳者追記: NBT compoundはNBTの**中括弧{}で囲まれた、中に複数のデータが入る配列のこと。**リスト[]ではない。)
- 専用NBT compoundは接頭辞か、ほかのバニラと競合しない命名規則で名付けられているcompound内に存在する。
- (注記: カスタムアイテムタグとは、アイテムの「`tag`」NBT内のバニラ以外のタグのこと。)
- (訳者追記: アイテムの「`tag`」NBT内は、好きなNBTを入れることができる。)

根拠:

- 他の準拠モジュールとカスタムアイテムタグが競合する可能性を効果的に下げられる。
- 規格に準拠しないパックのカスタムアイテムタグが競合する可能性を大幅に下げられる。
- 現在・将来のバニラNBTとカスタムアイテムタグが競合する可能性を大幅に下げられる。

注意事項:

- 「`CustomModelData`」はバニラタグであり、カスタムアイテムタグとは別物のため、この仕様に含まれない。.

推奨:

- NBT compoundの先頭にアンダースコアをつける(\_名前空間)か、別のカスタムcompound「`__custom__.名前空間`」の中に入れておくと、バニラタグから分離された状態を維持することができる

例:

- 悪: `{tag: {fire_wand: true}}`
- 悪: `{tag: {_fire_wand: true}}`
- 悪: `{tag: {mystuff: {fire_wand: true}}}`
- 正: `{tag: {_mystuff: {fire_wand: true}}}`
- 正: `{tag: {__custom__: {mystuff: {fire_wand: true}}}}`

### 2c. スコアボードのオブジェクトの名前空間

定義:

- すべてのスコアボードのオブジェクトには、接頭辞としてスコア空間:が付けられる。これはオブジェクト名が16文字までであることを考慮した、モジュールの名前空間の省略版でよい。
- (訳者追記: 要するに、名前空間の代わりに短くしたもの(スコア空間)を使うということ。例えば私は名前空間にckenja:を使うのでスコア空間にはckj:などを使う)
- (訳者追記: スコアボードのオブジェクトがわからない人もいるだろう。scoreboardコマンドを実行するときに必要なスコア名だ。
- (　　　　  `scoreboard objectives add \[ここだよ!\]`
- (　　　　  `scoreboard players set \[ここだよ!\] @s` )
- (訳者追記: 1.18より、オブジェクト名の16文字の制限が撤廃された。これにより省略版ではなく完全版のモジュールの名前空間を記載することができるようになった。
-  　　　　  ただし、後方互換性を考慮すると、1.18以降でしか絶対に動かないパック以外はこれまで通り16文字以内のオブジェクト名をつけるといいだろう)

根拠:

- 他の準拠モジュールとスコアボードのオブジェクトが競合する可能性をほぼなくせる。
- 規格に準拠しないパックとスコアボードのオブジェクトが競合する可能性を大幅に下げられる。
- スコアボード操作にスコープを導入し、ダミープレイヤーの変数名を短縮できるようになる。
- (訳者追記: ダミープレイヤーの変数名とは、scoreboardのセレクタなどの代わりに#や$から始まるプレイヤー名を使うこと。#や$から始まるプレイヤー名は存在しないので、エンティティがいなくても変数として使える)

警告:

- \*スコアボードのオブジェクト名前は長さに制限があるため、使えるスコア空間の長さは名前空間の長さよりはるかに短い。よって二つのスコア空間が競合する可能性は完全な名前空間の場合よりも高くなる。

推奨:

- モジュールの完全な名前空間の省略版と容易に連想できるスコア空間を使う。短すぎる略語や一般的な単語になる略語は避ける。
- オブジェクトが明白な名前だからと言って、それがほかのパックに(間違って)使われることはないと思ってはいけない。例えば`global`というオブジェクトは一般的にグローバル変数を登録するために使われやすく、競合しやすい。準拠したモジュールでは、`[スコア空間].global`のような独自のオブジェクトを使って競合を回避し、ダミープレイヤーの名前を短くすることができる。

例:

- 悪: `global`
- 悪: `temp`
- 悪: `const`
- 悪: `ptrak_config`
- 正: `ptrak.config`
- 正: `ptrak.deathtime`
- 正: `ptrak.sincerest`
- 正: `ptrak.usebow`

### 2d. Namespace NBT storage locations

Definition:

- All NBT storage locations use the module namespace as part of their own namespace.

Rationale:

- Effectively eliminates the chance of NBT storage conflicts with other compliant modules.
- Drastically reduces the chance of NBT storage conflicts with packs that do not follow any standards.

Recommendations:

- Use the same namespace as your module for any persistent data, such as configuration options.
- Use a separate namespace based on the original namespace (such as `<namespace>.__temp__`) for any temporary/transient data.
  - Reduces the risk of bloating and potentially corrupting the main storage.
  - Makes it easy to recognize which `.nbt` files can be safely purged.
  - Simplifies the process of debugging each storage individually.

Examples:

- Bad: `global`
- Bad: `main`
- Bad: `config`
- Bad: `config:playtracker`
- Good: `playtracker:config`
- Good: `playtracker.__temp__:player/on_sneak`
