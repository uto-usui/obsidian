---
alias: Dataview完全ガイド：ノートをデータベースに変える方法
tags:
  - Obsidian
  - memo
  - Dataview
  - guide
date: 2025-07-05
---

# Dataview完全ガイド：ノートをデータベースに変える方法

## フェーズ1: 基礎の徹底解説

### 1-1: Dataviewとは何か？

Dataviewは、あなたのObsidian Vaultを、単なるノートの集合体から、柔軟でクエリ可能な**データベース**へと変貌させる強力なプラグインです。ファイル名、タグ、Properties（フロントマター）といったメタデータを活用し、動的なリスト、テーブル、タスク一覧などを自動で生成します。

Dataviewには、大きく分けて2つのクエリ言語が存在します。

*   **DQL (Dataview Query Language)**: SQLに似た、シンプルで直感的な構文を持つクエリ言語。本ガイドの主役です。
*   **Dataview JS**: JavaScriptを用いて、より複雑で高度な処理を記述するための上級者向けオプション。

このプラグインの思想は、「ノートを構造化し、その構造を利用して情報を再発見し、繋げる」ことにあります。

### 1-2: 基本的なクエリ構造

DQLは、コードブロック内に `dataview` と指定することで記述します。

````markdown
```dataview
[クエリの種類]
FROM [データの取得元]
WHERE [絞り込み条件]
SORT [並べ替え条件]
```
````

#### クエリの種類 (View Types)

| 種類   | 説明                                                         |
| :----- | :----------------------------------------------------------- |
| `TABLE`  | 指定したフィールド（列）を持つテーブル形式で表示します。最もよく使われる形式です。 |
| `LIST`   | シンプルな箇条書きリストで表示します。ファイル名が自動的に表示されます。 |
| `TASK`   | Vault内にある未完了のタスク（`- [ ]`）をチェックボックス付きで一覧表示します。 |

#### FROM句：データの取得元を指定

どこからデータを取得するかを指定します。複数指定も可能です。

*   **フォルダ**: `FROM "10_Projects"`
*   **タグ**: `FROM #book-review`
*   **インリンク**: `FROM [[目標設定]]` （「目標設定」ノートにリンクしている全てのノート）
*   **アウトリンク**: `FROM outgoing([[目標設定]])` （「目標設定」ノートがリンクしている全てのノート）
*   **複数指定**: `FROM #book-review AND #favorite` （両方のタグを持つノート）

#### WHERE句：データの絞り込み

取得したデータを、さらに特定の条件でフィルタリングします。

*   **比較演算子**: `WHERE rating > 4` （評価が4より大きい）
*   **文字列の部分一致**: `WHERE contains(file.name, "レシピ")` （ファイル名に「レシピ」を含む）
*   **nullチェック**: `WHERE due != null` （`due`というプロパティが存在する）
*   **論理演算子**: `WHERE (status = "in progress") AND (priority = "high")`

#### SORT句：データの並べ替え

結果を指定したフィールドで並べ替えます。

*   **昇順**: `SORT file.name ASC` （ファイル名で昇順）
*   **降順**: `SORT rating DESC` （評価で降順）

#### LIMIT句：表示件数の制限

クエリ結果の表示件数を制限したい場合に使用します。

*   `LIMIT 10` （最大10件まで表示）

#### GROUP BY句：データのグルーピング

特定のフィールドの値に基づいて、結果をグループ化します。例えば、ノートをカテゴリごとに分類して表示したい場合に非常に強力です。

*   `GROUP BY status` （`status`プロパティの値でグループ化）

```dataview
TABLE rows.file.name AS "ファイル名"
FROM "20_Areas/Health"
GROUP BY category
```
上記の例では、「Health」エリアのノートを`category`プロパティの値（例: "運動", "食事"）でグループ化し、テーブル表示します。

---

## フェーズ2: メタデータとDQL関数

### 2-1: 暗黙的なフィールド（Implicit Fields）

Dataviewは、各ノートに自動的に付与される「暗黙的なフィールド」を持っています。これらは、明示的にPropertiesとして定義していなくても、クエリで利用できます。

| フィールド名     | 説明                                                         |
| :--------------- | :----------------------------------------------------------- |
| `file.name`      | ノートのファイル名（拡張子なし）                             |
| `file.path`      | ノートの絶対パス                                             |
| `file.folder`    | ノートが格納されているフォルダのパス                         |
| `file.ctime`     | ノートの作成日時                                             |
| `file.mtime`     | ノートの最終更新日時                                         |
| `file.size`      | ノートのファイルサイズ（バイト）                             |
| `file.tags`      | ノートに付与されている全てのタグのリスト                     |
| `file.etags`     | ノートに付与されている全ての明示的なタグのリスト（サブタグを含む） |
| `file.aliases`   | ノートのエイリアス（別名）のリスト                           |
| `file.inlinks`   | このノートへのバックリンクのリスト                           |
| `file.outlinks`  | このノートからのアウトゴーイングリンクのリスト               |
| `file.tasks`     | ノート内のタスクのリスト                                     |

これらのフィールドは、特に`FROM`句や`WHERE`句でノートを絞り込んだり、`TABLE`で表示したりする際に非常に便利です。

### 2-2: Properties（YAMLフロントマター）の活用

ObsidianのProperties機能は、Dataviewと組み合わせて使うことで、ノートのメタデータを強力に構造化できます。Propertiesは、ノートの先頭に`---`で囲んで記述するYAML形式のデータです。

```yaml
---
title: "Dataview完全ガイド"
status: "in progress"
date: 2025-07-05
tags: [Obsidian, memo, Dataview]
author: "Gemini"
rating: 5
---
```

Dataviewは、これらのPropertiesを自動的に読み込み、クエリの対象として利用できます。例えば、上記のノートであれば、`title`, `status`, `date`, `tags`, `author`, `rating`といったフィールドをクエリで参照できます。

*   **データ型の自動認識**: Dataviewは、Propertiesの値の型（テキスト、数値、日付、リストなど）を自動的に認識します。これにより、数値比較や日付計算などが可能です。
*   **リスト形式のProperties**: `tags: [Obsidian, memo, Dataview]` のようにリスト形式で記述されたPropertiesは、`contains()`関数などと組み合わせて、特定の要素が含まれるかをチェックできます。

---

### 2-3: 主要なDQL関数

Dataviewには、クエリ内でデータを加工するための様々な関数が用意されています。ここでは、特によく使うものを紹介します。

#### 文字列操作

*   `length(string)`: 文字列の長さを返します。
*   `substring(string, start, end)`: 文字列の一部を抽出します。
*   `replace(string, pattern, replacement)`: 文字列内のパターンを置換します。
*   `lower(string)` / `upper(string)`: 小文字/大文字に変換します。

#### 日付操作

*   `date(string)`: 文字列を日付オブジェクトに変換します。
*   `dur(string)`: 期間（duration）オブジェクトを作成します。（例: `dur("1 day")`）
*   `dateformat(date, format)`: 日付を指定した形式でフォーマットします。
*   `now()`: 現在の日時を返します。

#### 配列操作

*   `choice(condition, value1, value2)`: 条件に基づいて2つの値のいずれかを返します。（三項演算子のようなもの）
*   `contains(array|string, value)`: 配列または文字列が特定の値を含むかをチェックします。
*   `join(array, separator)`: 配列の要素を結合して文字列を生成します。

#### オブジェクト操作

*   `object(key1, value1, key2, value2, ...)`: キーと値のペアからオブジェクトを作成します。

---

## フェーズ3: 実践的クエリとユースケース

Dataviewの真価は、日々のワークフローに組み込むことで発揮されます。ここでは、具体的なユースケースに合わせたクエリ例を紹介します。

### 3-1: プロジェクト管理（P.A.R.A.メソッドとの連携）

P.A.R.A.メソッドで管理しているプロジェクトをDataviewで一覧化し、進捗を可視化します。

**進行中のプロジェクト一覧**

`10_Projects`フォルダ内のノートで、`status`が`"in progress"`のものを表示します。`due`プロパティで期限を設定しておくと、並べ替えに便利です。

````markdown
```dataview
TABLE status as "ステータス", due as "期限", file.mtime as "最終更新"
FROM "10_Projects"
WHERE status = "in progress"
SORT due ASC
```
````

**期限切れタスクの警告リスト**

Vault全体から、期限が過ぎた未完了のタスクを抽出します。`due`プロパティが日付形式で設定されていることを前提とします。

````markdown
```dataview
TASK
FROM ""
WHERE !completed AND due < date(now())
SORT due ASC
```
````

### 3-2: ナレッジマネジメント

読書記録や特定のトピックに関するノートをDataviewで整理し、知識の再発見を促します。

**読書記録テーブル**

`#book-review`タグが付与されたノートから、書籍情報をテーブル表示します。`rating`プロパティで評価を付けておくと、ソートに利用できます。

````markdown
```dataview
TABLE author as "著者", rating as "評価", read_date as "読了日"
FROM #book-review
SORT rating DESC
```
````

**特定のトピックに関連するノートの自動集約（MOCの自動生成）**

例えば、`#AI`タグが付与された全てのノートを自動的に一覧表示するMOC（Map of Content）を生成します。これにより、関連する知識が散逸するのを防ぎます。

````markdown
```dataview
LIST
FROM #AI
SORT file.name ASC
```
````

---

### 3-3: ライフログ

日々の活動や記録をDataviewで整理し、振り返りを容易にします。

**習慣トラッカー**

日次ノート（例: `YYYY-MM-DD.md`）に習慣の達成状況をPropertiesとして記録している場合、Dataviewで一覧表示できます。例えば、`daily_note`タグが付与されたノートで`meditation`と`exercise`のプロパティを追跡する場合。

````markdown
```dataview
TABLE meditation as "瞑想", exercise as "運動"
FROM #daily_note
SORT file.name DESC
LIMIT 7
```
````

**議事録インデックス**

会議の議事録ノートに`meeting_date`や`attendees`などのPropertiesを設定している場合、Dataviewで議事録の一覧を簡単に作成できます。

````markdown
```dataview
TABLE meeting_date as "日付", attendees as "参加者", file.name as "議事録"
FROM "Meetings"
SORT meeting_date DESC
```
````

---

## フェーズ4: Dataview JSへの入門

Dataview JSは、DQLでは表現が難しい複雑なロジックや、より高度なデータ操作を行いたい場合に利用します。JavaScriptの知識が必要になりますが、その分、Dataviewの可能性を大きく広げることができます。

### 4-1: Dataview JSとは？

DQLがSQLライクな宣言的なクエリであるのに対し、Dataview JSはJavaScriptのコードとして記述します。ObsidianのAPI（`dv`オブジェクト）を通じて、Vault内のノートやそのメタデータにアクセスし、JavaScriptの柔軟な機能を使ってデータを処理・表示します。

**DQLとの違いと、利用するメリット**

| 項目       | DQL                                  | Dataview JS                                  |
| :--------- | :----------------------------------- | :------------------------------------------- |
| **構文**   | SQLライクな宣言型                    | JavaScriptコード                             |
| **複雑性** | シンプルなクエリに適している         | 複雑なロジックや条件分岐が可能               |
| **学習コスト** | 低い                                 | JavaScriptの知識が必要                       |
| **柔軟性** | 限定的                               | 非常に高い（JavaScriptの機能全てを利用可能） |

Dataview JSの主なメリットは、DQLでは不可能な**条件分岐**、**ループ処理**、**外部ライブラリとの連携**など、プログラミングの自由度を活かしたデータ操作ができる点です。

### 4-2: 簡単なスクリプト例

Dataview JSは、`dataviewjs`コードブロック内に記述します。

**特定のタグを持つノートを条件付きで表示**

例えば、`#project`タグを持つノートのうち、`status`が`"completed"`ではないものをリスト表示し、期限が近いものには警告を表示するスクリプトです。

````javascript
```dataviewjs
let pages = dv.pages("#project")
  .where(p => p.status !== "completed");

dv.list(pages.map(p => {
  let output = p.file.link;
  if (p.due && p.due.diff(dv.current().file.day, "days").days < 7) {
    output += " ⚠️ (期限間近)";
  }
  return output;
}));
```
````

*   `dv.pages("#project")`: `#project`タグを持つ全てのノートを取得します。
*   `.where(p => p.status !== "completed")`: 取得したノートの中から、`status`プロパティが`"completed"`ではないものをフィルタリングします。
*   `.map(p => { ... })`: 各ノートに対して処理を行い、新しい配列を生成します。ここでは、期限が近い場合に警告マークを追加しています。
*   `dv.list()`: 処理結果をリスト形式で表示します。

---