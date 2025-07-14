# Claude Code Hooks: Stop

tags: #claude #hooks #how-to #解説

このドキュメントでは、Claude Codeの `Stop` フックの使い方について解説します。

## 【解説】Stopフックとは？

`Stop`フックは、メインのClaude Codeエージェントが応答を完了し、処理を終了する際にトリガーされる機能です。ユーザーとの一連の対話が完了したタイミングで、クリーンアップ処理やセッションの要約、最終的な成果物の保存など、定型的な後処理を自動的に実行させたい場合に非常に役立ちます。

このフックは、Claudeが「作業完了」と判断した最後の瞬間に一度だけ呼び出されます。具体的な活用例は以下の通りです。

*   **クリーンアップ:** 対話中に作成された一時ファイルやディレクトリ（例: `/tmp/claude_session_*`）を自動的に削除する。
*   **成果物のコミット:** `obsidian-git`のようなプラグインと連携し、一連の作業が完了した時点で変更を自動的にGitにコミットする。
*   **セッションの要約:** そのセッションで行われた作業内容のサマリーを生成し、ログファイルや日報に記録する。
*   **リソースの解放:** 対話中に確保したリソース（例: データベース接続、バックグラウンドプロセス）を確実に解放する。

## 【ハウツー】Stopフックの設定方法

設定は、他のフックと同様に `/hooks` コマンドまたは `settings.json` の編集で行います。

### `settings.json` での設定例

`Stop`フックには、他のフックのような詳細なマッチャーや引数はありません。エージェントの停止という単一のイベントに対してコマンドを実行する、シンプルな構造になっています。

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "your_cleanup_script.sh"
      }
    ]
  }
}
```

**説明:**

*   `command`: エージェントが停止する際に実行したいシェルスクリプトやコマンドを指定します。

## 実用例

### 例1: 一時ファイルのクリーンアップ

セッション中に作成された `*.tmp` ファイルを、対話の最後にまとめて削除するフックです。

**スクリプト (`scripts/cleanup_temp_files.sh`):**

```bash
#!/bin/bash

TEMP_DIR="/path/to/your/temp/directory"

# TEMP_DIR 内の .tmp ファイルを検索して削除
find "$TEMP_DIR" -name "*.tmp" -type f -delete

echo "Temporary files cleaned up."
```

**`settings.json`:**

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "scripts/cleanup_temp_files.sh"
      }
    ]
  }
}
```

**説明:**

*   Claudeとの対話が終了すると、`cleanup_temp_files.sh` が実行されます。
*   このスクリプトは、指定されたディレクトリ内のすべての一時ファイル（`.tmp`）を検索し、安全に削除します。

### 例2: 作業完了時にGitで自動コミット

`obsidian-git`プラグインが導入されているObsidian Vaultで、Claudeとの作業完了時に変更内容を自動でコミットするフックです。

**前提:**
*   `obsidian-git`がインストールされ、設定済みであること。
*   コマンドラインから `git` コマンドが利用可能であること。

**`settings.json`:**

```json
{
  "hooks": {
    "Stop": [
      {
        "command": "(cd /Users/usui.y/work/uto/obsidian/uto-notebook && git add . && git commit -m \"Auto-commit by Claude at end of session\")"
      }
    ]
  }
}
```

**説明:**

*   `cd /path/to/your/vault`: まず、GitリポジトリであるObsidian Vaultのディレクトリに移動します。
*   `git add .`: すべての変更をステージングします。
*   `git commit -m "..."`: 固定のメッセージでコミットを実行します。
*   コマンド全体を `()` で囲むことで、サブシェルで実行され、`cd` の影響が現在のセッションに残らないようにしています。

---

`Stop`フックは、一連の作業の締めくくりを自動化し、クリーンで整理された状態を維持するための強力なツールです.
