# Claude Code Hooks: SubagentStop

tags: #claude #hooks #how-to #解説

このドキュメントでは、Claude Codeの `SubagentStop` フックの使い方について解説します。

## 【解説】SubagentStopフックとは？

`SubagentStop`フックは、`Task` のように、メインのエージェントから起動されたサブエージェントがその処理を完了した際にトリガーされる機能です。これは、より大きなタスクの中の特定のサブタスクが完了した直後に、後処理や検証を行いたい場合に特に有効です。

`Stop`フックがメインエージェント全体の終了を捕捉するのに対し、`SubagentStop`フックは個々のサブエージェントの終了を捕捉します。これにより、より細かい粒度での自動化が可能になります。

主な活用例は以下の通りです。

*   **サブタスクごとの成果物検証:** ある `Task` がファイルを生成した場合、その `Task` の完了直後にスクリプトを実行し、生成されたファイルが期待通りの形式になっているか検証する。
*   **部分的なテスト実行:** サブエージェントが特定のモジュールに変更を加えた場合、そのモジュールに関連するテストのみを即座に実行する。
*   **進捗のロギング:** 複数の `Task` で構成される大規模な作業において、各 `Task` の完了をログに記録し、進捗を詳細に追跡する。

## 【ハウツー】SubagentStopフックの設定方法

設定は、他のフックと同様に `/hooks` コマンドまたは `settings.json` の編集で行います。

### `settings.json` での設定例

`SubagentStop`フックも `Stop`フックと同様、詳細なマッチャーはなく、サブエージェントの停止というイベントに対してコマンドを実行するシンプルな構造です。

```json
{
  "hooks": {
    "SubagentStop": [
      {
        "command": "your_post_subtask_script.sh"
      }
    ]
  }
}
```

**説明:**

*   `command`: サブエージェントが停止する際に実行したいシェルスクリプトやコマンドを指定します。

## 実用例

### 例: `Task`完了後のファイル検証

ある`Task`が `data.json` というファイルを生成するシナリオを考えます。その`Task`が完了した直後に、生成されたJSONが妥当な形式であるかを `jq` コマンドを使って検証するフックです。

**スクリプト (`scripts/validate_json.sh`):**

```bash
#!/bin/bash

JSON_FILE="/path/to/your/project/data.json"

# jqを使ってJSONの構文をチェック
if jq -e . "$JSON_FILE" >/dev/null 2>&1; then
  echo "Validation successful: $JSON_FILE is a valid JSON."
  # ここで成功通知を送るなどの追加アクションも可能
else
  echo "Validation FAILED: $JSON_FILE is not a valid JSON."
  # ここでエラー通知を送るなどの追加アクションも可能
fi
```

**`settings.json`:**

```json
{
  "hooks": {
    "SubagentStop": [
      {
        "command": "scripts/validate_json.sh"
      }
    ]
  }
}
```

**説明:**

*   Claudeに「`data.json`を作成するタスク」を依頼し、`Task`ツールが使用されるとします。
*   その`Task`が完了した時点で `SubagentStop` フックがトリガーされ、`validate_json.sh` が実行されます。
*   スクリプトは `jq` コマンドを利用して `data.json` の構文をチェックし、結果をコンソールに出力します。
*   `-e` オプションは、`jq`がエラーなく処理を終えた場合に終了コード `0` を返すようにします。

---

`SubagentStop`フックを利用することで、複雑なワークフローの各ステップをきめ細かく制御し、タスクの信頼性と品質を向上させることができます。
