---
alias: "タグ活用術"
---
# タグ活用術：情報を分類し、自在に引き出す

## 1. タグの基本：ノートに属性を付与する

タグは、ノートに特定のキーワード（属性）を付与し、分類・整理するための機能です。`#`（ハッシュ）記号に続けてテキストを入力することで、簡単に作成できます。

*   **基本構文**: `#アイデア` `#読書記録`
*   **命名規則**:
    *   スペースは含められません。複数単語を使いたい場合は、`#プロジェクト管理` のように連結するか、キャメルケース (`#projectManagement`)、ケバブケース (`#project-management`) などを活用します。
    *   大文字・小文字は区別されません（`#Book` と `#book` は同じタグとして扱われます）。一貫性を保つため、**すべて小文字**で統一するのがおすすめです。

## 2. タグの記述場所：フロントマター vs インライン

タグをどこに書くかは、ワークフローの根幹に関わる重要な選択です。主に2つの方法があり、それぞれに利点があります。

### A. フロントマター（Properties）に記述する

ノートの先頭に `---` で囲まれたエリア（YAMLフロントマター）に記述します。ObsidianのProperties機能を使えば、UIで簡単に入力できます。

```yaml
---
tags:
  - project/work
  - status/inprogress
---
```

*   **特徴**: ノートの「本文」と、そのノートの属性情報である「タグ」を明確に分離できます。ノート全体の種類（例: 読書メモ）や状態（例: 進行中）といった、**公式な属性**を定義するのに適しています。

### B. インライン（本文中）に記述する

文章を書きながら、文脈に応じて `#` を付けて記述します。

```
...今日の会議で、新しい #project/work の立ち上げが決まった。まずは要件定義から始める必要がある。#status/todo
```

*   **特徴**: タグが記述された場所の文脈と強く結びつきます。思考の流れを止めずに、その場限りの**有機的なマーカー**（例: `#あとで確認`）として使うのに便利です。

## 3. 階層型タグ：情報を体系化する

タグは `/` で区切ることで、階層構造を持たせることができます。これにより、より体系的で強力な分類が可能になります。

*   **構文**: `#親カテゴリ/子カテゴリ`
*   **例**: `#status/1_todo`, `#source/book`, `#project/work/A案件`
*   **メリット**: 親タグ（例: `#status`）で検索すると、その配下にあるすべての子タグが付与されたノートがヒットするため、柔軟な検索が可能になります。

## 4. タグ vs. リンク：思想の違いを理解する

タグとリンクは似て非なるものです。その違いを理解し、使い分けることが重要です。

| 特徴 | リンク `[[ノート名]]` | タグ `#キーワード` |
| :--- | :--- | :--- |
| **目的** | **ノート間の具体的な関係性**を示す | **ノートの属性・カテゴリ**を付与する |
| **実体** | リンク先のノートという**実体を持つ** | キーワードという**実体を持たないメタデータ** |

**使い分けのヒント**: そのキーワードについて、**将来的に独立したノートとして書きたい**と思ったら**リンク**を、単に**分類・検索するための目印**なら**タグ**を使いましょう。

## 5. 戦略的タグ活用術：ハイブリッドアプローチ

最も強力で実践的なのは、フロントマターとインラインを組み合わせた**ハイブリッドアプローチ**です。

1.  **フロントマター**: そのノートの**全体的な属性**を示す、重要で固定的なタグを記述します。（例: `tags: [book-review, status/done]`）
2.  **インライン**: 本文中の**特定の文脈**で意味を持つ、一時的または補足的なタグを記述します。（例: `...この部分は後で再考が必要だ。#要検討`）

この使い分けにより、**構造的な整理**と**文脈に沿った柔軟なラベリング**を両立できます。

### その他のベストプラクティス

*   **一貫性のある命名規則**: 自分なりのルール（単数形か複数形か、など）を定め、一貫させましょう。
*   **`Tag Wrangler` プラグイン**: タグの一括リネームやマージなど、タグのメンテナンスを強力にサポートしてくれる必須プラグインです。
