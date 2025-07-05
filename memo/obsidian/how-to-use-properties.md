---
alias: Obsidian Propertiesの効果的な使い方
tags:
  - Obsidian
  - memo
date: 2025-07-05
---

# Obsidian Propertiesの効果的な使い方

Obsidianの**Properties**機能は、ノートに構造化されたメタデータを追加するための強力なツールです。この記事では、Propertiesの基本的な使い方から、具体的な活用例、さらにはDataviewプラグインとの連携方法までを詳しく解説します。

## 1. Propertiesとは？

Propertiesは、ノートに関する情報（メタデータ）をキーと値のペアで管理する機能です。これにより、ノートの作成日、ステータス、関連情報などを体系的に整理できます。

### YAMLフロントマターとの違い

- **UIの提供**: Propertiesは専用のUIを通じて、YAMLフロントマターをより直感的に編集できるようにしたものです。
- **データ型のサポート**: 日付ピッカーやチェックボックスなど、特定のデータ型に合わせた入力支援機能が提供されます。

## 2. 基本的な使い方

Propertiesは、ノートの最上部に表示される専用のUI、または直接Markdownを編集することで追加・編集できます。

### Propertiesの追加方法

1.  **コマンドパレット**: `Ctrl/Cmd + P`でコマンドパレットを開き、「`Add file property`」を実行します。
2.  **UIから**: ノート上部の「`+ Add property`」ボタンをクリックします。
3.  **手動**: ノートの先頭に`---`で囲まれた領域（フロントマター）を作成し、`キー: 値`の形式で記述します。

```yaml
---
tags: [Obsidian, memo]
date: 2025-07-05
status: "Published"
---
```

### 利用可能なデータ型

| データ型     | 説明                               | 例                               |
| :----------- | :--------------------------------- | :------------------------------- |
| **テキスト** | 文字列を格納します。               | `author: "John Doe"`             |
| **リスト**   | 複数の値を格納します。             | `tags: [Obsidian, memo, guide]`  |
| **数値**     | 数値データを格納します。           | `version: 1.2`                   |
| **日付**     | 日付と時刻を格納します。           | `created: "2025-07-05T10:00:00"` |
| **チェックボックス** | 真偽値（true/false）を格納します。 | `reviewed: true`                 |

## 3. 具体的な活用例

### ノートのステータス管理

`status`プロパティを追加することで、ノートの執筆状況を管理できます。

```yaml
---
status: "in progress" # "idea", "draft", "published" など
---
```

### 関連情報のリンク

`related`プロパティに内部リンクをリスト形式で追加し、関連ノートへのナビゲーションを容易にします。

```yaml
---
related:
  - "[[how-to-use-backlinks-and-outgoing-links]]"
  - "[[practical-guide-to-zettelkasten]]"
---
```

## 4. Dataviewプラグインとの連携

[Dataview](https://blacksmithgu.github.io/obsidian-dataview/)プラグインと組み合わせることで、Propertiesの真価が発揮されます。Propertiesの情報をクエリとして使用し、ノートを動的に一覧表示できます。

### 例：特定のタグを持つノートを一覧表示

`memo/obsidian`フォルダ内で、`Obsidian`タグを持ち、かつ`status`が`"published"`ではないノートをテーブル形式で表示します。

````markdown
```dataview
TABLE date, status
FROM "memo/obsidian"
WHERE contains(file.tags, "Obsidian") AND status != "published"
SORT file.mtime desc
```
````

このように、Propertiesを使いこなすことで、Obsidianでの知識管理がより体系的かつ効率的になります。
