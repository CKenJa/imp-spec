# IMP 標準コア(IMP-Core)

以下はデータパック・モジュールがIMPに準拠していると言える最低限の基準である。

- [1. バニラには手を付けない](#1-バニラには手を付けない)
- [2. 全てに名前空間を](#2-全てに名前空間を)
  - [2a. エンティティタグの名前空間](#2a-エンティティタグの名前空間)
  - [2b. カスタムアイテムタグの名前空間](#2b-カスタムアイテムタグの名前空間)
  - [2c. スコアボードオブジェクトの名前空間](#2c-スコアボードオブジェクトの名前空間)
  - [2d. storageの名前空間](#2d-storageの名前空間)

## 1. バニラには手を付けない

意味:

- 出来る限りバニラの`minecraft`名前空間下のものを変更しないこと。

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

意味:

- 全てのエンティティタグにはモジュール名前空間の接頭辞と区切りが付けられます
- (ノート: エンティティタグ `tag`コマンドで扱う、`Tags`NBT内に保存されるタグです)

根拠:

- 他の準拠モジュールとエンティティタグの競合を効果的に回避できる。
- Drastically reduces the chance of entity tag conflicts with packs that do not follow any standards.

Caveats:

- The character `:` is not supported in all the same places tags are and thus cannot be used as a namespace delimiter.

Recommendations:

- Use `.` as the delimiter for both the namespace and different levels of tags, and use `_` for separating words (snake_case).
- Be verbose with your tags. Do not sacrifice your project's readability in the never-ending struggle for micro-optimization. If performance ever becomes a concern, run your pack through a minimizer and distribute the minimized version to your end users.

Examples:

- Bad: `PlaytrackerIgnore`
- Bad: `playtracker_ignore`
- Good: `playtracker.ignore`
- Good: `playtracker.talked_to_villager`
- Good: `playtracker.sneaking.stopped`

### 2b. Namespace custom item tags

Definition:

- All custom item tags are contained inside an NBT compound that is named after and dedicated to the module.
- The dedicated NBT compound is prefixed or exists within another compound with a naming convention that does not conflict with vanilla.
- (Note: custom item tags are any non-vanilla tags stored within an item's `tag` NBT.)

Rationale:

- Effectively eliminates the chance of custom item tag conflicts with other compliant modules.
- Drastically reduces the chance of custom item tag conflicts with packs that do not follow any standards.
- Drastically reduces the chance of custom item tag conflicts with any current and/or future vanilla NBT.

Caveats:

- The `CustomModelData` tag is a vanilla tag and is not part of this specification.

Recommendations:

- Prefix your NBT compound with an underscore `_<namespace>` or keep it within another custom compound `__custom__.<namespace>` to help ensure that it remains separate from vanilla tags.

Examples:

- Bad: `{tag: {fire_wand: true}}`
- Bad: `{tag: {_fire_wand: true}}`
- Bad: `{tag: {mystuff: {fire_wand: true}}}`
- Good: `{tag: {_mystuff: {fire_wand: true}}}`
- Good: `{tag: {__custom__: {mystuff: {fire_wand: true}}}}`

### 2c. Namespace scoreboard objectives

Definition:

- All scoreboard objectives are prefixed with a _scorespace_: an abbreviated version of the module namespace that accounts for the 16-character limit on objective names.

Rationale:

- Effectively eliminates\* the chance of scoreboard objective conflicts with other compliant modules.
- Drastically reduces the chance of scoreboard objective conflicts with packs that do not follow any standards.
- Introduces a level of scope to scoreboard operations, allowing fake-player variable names to be shortened.

Caveats:

- \*Because the names of scoreboard objectives are limited in length, the number of possible scorespaces is much smaller than the number of possible namespaces. Therefore, the chance of a collision between two scorespaces is higher than it would be with the full namespaces.

Recommendations:

- Use a scorespace that is easily associated with the full namespace of the module. Avoid abbreviations that are too short or generic.
- Do not assume that just because the objective is named something obvious it will not be (mis)used by other packs. For example: the objective `global` is commonly used to register global variables, but this is precisely what makes it all the more susceptible to conflicts. A compliant module will use its own objective, such as `<scorespace>.global`, to both avoid conflicts and shorten the names of fake-players.

Examples:

- Bad: `global`
- Bad: `temp`
- Bad: `const`
- Bad: `ptrak_config`
- Good: `ptrak.config`
- Good: `ptrak.deathtime`
- Good: `ptrak.sincerest`
- Good: `ptrak.usebow`

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
