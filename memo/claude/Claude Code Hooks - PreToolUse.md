# Claude Code Hooks: PreToolUse

tags: #claude #hooks #how-to #解説

このドキュメントでは、Claude Codeの `PreToolUse` フックの使い方について解説します。

## 【解説】PreToolUseフックとは？

`PreToolUse`フックは、Claude Codeの強力な機能の一つで、ツールが使用される**前**に特定のシェルコマンドを実行することができます。これにより、独自のロジックに基づいてツールの実行を検証、変更、あるいはブロックするための重要なチェックポイントを設けることが可能になります。

このフックは、Claudeがツールの使用を決定した直後にトリガーされます。フックスクリプトは、提案されたツールのコマンドと引数を検査し、その情報に基づいて以下のアクションを実行できます。

1.  **コマンドを許可する:** 終了コード `0` でスクリプトを終了します。
2.  **コマンドをブロックする:** ゼロ以外の終了コードでスクリプトを終了します。スクリプトの標準出力がClaudeに表示され、コマンドをブロックした理由を伝えることができます。

この機能により、プロジェクト固有のルールの強制から、偶発的なデータ損失の防止まで、幅広いユースケースが実現可能です。

## 【ハウツー】PreToolUseフックの設定方法

フックの設定には、主に2つの方法があります。スラッシュコマンド `/hooks` を使用する方法と、`settings.json` ファイルを直接編集する方法です。

### 方法1: `/hooks` コマンドの使用（推奨）

`/hooks` コマンドは、フックを対話的に管理するための便利な方法です。

1.  チャットで `/hooks` と入力します。
2.  `PreToolUse` イベントを選択します。
3.  このフックを適用するツールを指定するための「マッチャー」を定義します。単純な文字列（例: `Bash`）や、より複雑なマッチングのための正規表現を使用できます。
4.  実行したいシェルコマンドを入力します。

### 方法2: `settings.json` の編集

より高度な設定を行いたい場合は、`settings.json` ファイルを直接編集します。このファイルは、ユーザーレベル、プロジェクトレベル、またはローカルレベルで存在します。

以下は、`settings.json` における `PreToolUse` フックの設定例です。

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": {
          "toolName": "Bash"
        },
        "command": "your_script.sh"
      }
    ]
  }
}
```

## 実用例

### 例1: 危険なコマンドのブロック

誤って `rm -rf` を使用してしまうのを防ぐためのフックを作成してみましょう。

**スクリプト (`scripts/pre_tool_check.sh`):**

```bash
#!/bin/bash

# 提案されたコマンドは最初の引数として渡される
PROPOSED_COMMAND="$1"

# コマンドに "rm -rf" が含まれているかチェック
if [[ "$PROPOSED_COMMAND" == *"rm -rf"* ]]; then
  echo "エラー: 安全のため 'rm -rf' コマンドはブロックされています。"
  exit 1 # コマンドをブロック
fi

exit 0 # コマンドを許可
```

**`settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": {
          "toolName": "Bash"
        },
        "command": "scripts/pre_tool_check.sh \"${tool_code}\""
      }
    ]
  }
}
```

これで、Claudeが `rm -rf` を実行しようとすると、フックがそれをブロックし、エラーメッセージを表示します。

### 例2: 実行前の確認要求

この例では、破壊的な可能性のあるコマンドを実行する前に、ユーザーに確認を求めるフックを作成する方法を示します。

**スクリプト (`scripts/confirm_execution.sh`):**

```bash
#!/bin/bash

PROPOSED_COMMAND="$1"

# 確認を求める
read -p "次のコマンドを実行しますか？ (y/n): $PROPOSED_COMMAND " -n 1 -r
echo

if [[ $REPLY =~ ^[Yy]$ ]]; then
  exit 0 # 実行
else
  echo "ユーザーによって実行がキャンセルされました。"
  exit 1 # キャンセル
fi
```

**`settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": {
          "toolName": "Bash",
          "code": ".*(mv|cp|rm).*" // mv, cp, rmにマッチする正規表現
        },
        "command": "scripts/confirm_execution.sh \"${tool_code}\""
      }
    ]
  }
}
```

このフックは、一般的なファイル操作に対する対話的な安全策を提供します。

---

`PreToolUse` フックを活用することで、Claude Codeを使ったワークフローの安全性と信頼性を大幅に向上させることができます。