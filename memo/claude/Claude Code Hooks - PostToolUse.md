# Claude Code Hooks: PostToolUse

tags: #claude #hooks #how-to #解説

このドキュメントでは、Claude Codeの `PostToolUse` フックの使い方について解説します。

## 【解説】PostToolUseフックとは？

`PostToolUse`フックは、ツールが正常に実行された**後**に、特定のシェルコマンドを自動で実行するための機能です。これにより、ツールの実行結果に基づいた後処理や、定型的なフォローアップ作業を自動化できます。

このフックは、Claudeがツールを正常に使用し終えた直後にトリガーされます。フックスクリプトは、実行されたツールの情報（ツール名、引数、出力など）を受け取り、それに基づいて様々な処理を行うことができます。

主な活用例としては、以下のような挙げられます。

*   **コードの自動フォーマット:** `Write` や `Edit` ツールでファイルが変更された後に、リンターやフォーマッター（例: `prettier`, `black`）を自動的に実行する。
*   **ログの記録:** 特定のツールが使用された日時や結果をログファイルに記録する。
*   **テストの実行:** コードが変更された後に、関連するユニットテストを自動で実行する。
*   **通知:** ツールの実行結果をSlackやDiscordなどの外部サービスに通知する。

## 【ハウツー】PostToolUseフックの設定方法

設定方法は `PreToolUse` フックと同様に、`/hooks` コマンドまたは `settings.json` の直接編集で行います。

### 方法1: `/hooks` コマンドの使用

1.  チャットで `/hooks` と入力します。
2.  `PostToolUse` イベントを選択します。
3.  フックを適用するツールを指定する「マッチャー」を定義します。
4.  実行したいシェルコマンドを入力します。

### 方法2: `settings.json` の編集

`settings.json` に以下のような設定を記述します。

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": {
          "toolName": "Write"
        },
        "command": "your_script.sh"
      }
    ]
  }
}
```

## 実用例

### 例1: ファイル書き込み後の自動フォーマット

`Write` ツールで `.js` ファイルが書き込まれた後に、`prettier` を使って自動的にコードをフォーマットするフックを作成します。

**`settings.json`:**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": {
          "toolName": "Write",
          "args": {
            "file_path": ".*\\.js$" // .jsファイルにのみマッチ
          }
        },
        "command": "npx prettier --write \"${tool_args.file_path}\""
      }
    ]
  }
}
```

**説明:**

*   `matcher.toolName`: `Write` ツールを指定します。
*   `matcher.args.file_path`: `file_path` 引数が `.js` で終わる場合にのみフックがトリガーされるように、正規表現でフィルタリングします。
*   `command`: `npx prettier --write` を実行します。`${tool_args.file_path}` という変数が、`Write` ツールに渡された `file_path` の値に自動的に置き換えられます。

### 例2: シェルコマンド実行のロギング

`Bash` ツールが使用されるたびに、実行されたコマンドと日時をログファイルに記録するフックを作成します。

**スクリプト (`scripts/log_bash_command.sh`):**

```bash
#!/bin/bash

LOG_FILE="/path/to/your/bash_history.log"
COMMAND_EXECUTED="$1"

# 日時とコマンドをログファイルに追記
echo "$(date): $COMMAND_EXECUTED" >> "$LOG_FILE"
```

**`settings.json`:**

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": {
          "toolName": "Bash"
        },
        "command": "scripts/log_bash_command.sh \"${tool_code}\""
      }
    ]
  }
}
```

**説明:**

*   このフックは、`Bash` ツールが使われるたびに `log_bash_command.sh` を実行します。
*   `${tool_code}` 変数には、`Bash` ツールで実行されたコマンド全体が格納されています。
*   スクリプトは、そのコマンドをタイムスタンプと共に指定のログファイルに追記します。

---

`PostToolUse` フックを使いこなすことで、開発ワークフローにおける多くの手作業を自動化し、一貫性と効率を大幅に向上させることができます。
