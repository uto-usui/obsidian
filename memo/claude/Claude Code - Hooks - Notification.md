# Claude Code Hooks: Notification

tags: #claude #hooks #how-to #解説

このドキュメントでは、Claude Codeの `Notification` フックの使い方について解説します。

## 【解説】Notificationフックとは？

`Notification`フックは、Claude Codeがユーザーに通知を送信する際に、カスタムのシェルコマンドを割り込ませることができる機能です。これにより、標準の通知システムを拡張し、外部のサービスと連携させたり、通知内容に基づいて追加のアクションを実行したりすることが可能になります。

このフックは、Claudeが何らかの情報をユーザーに知らせようとするあらゆる場面でトリガーされます。例えば、以下のような状況で活用できます。

*   **カスタム通知システム:** Claudeからの通知を、macOSの通知センター、Slack、Discord、LINEなど、普段利用している他のプラットフォームに転送する。
*   **重要通知のハイライト:** 通知のタイトルや内容に特定のキーワード（例: "Error", "Success"）が含まれている場合に、特別な音声やアラートを鳴らして注意を喚起する。
*   **通知のロギング:** すべての通知をファイルに記録し、後から参照できるようにする。

## 【ハウツー】Notificationフックの設定方法

設定は、これまでのフックと同様に `/hooks` コマンドまたは `settings.json` の編集で行います。

### `settings.json` での設定例

以下は、`settings.json` ファイルに直接記述する設定の例です。この設定は、`hooks` オブジェクト内に `Notification` フックの定義を追加します。

```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": {},
        "command": "your_notification_script.sh \"${title}\" \"${message}\"
      }
    ]
  }
}
```

**説明:**

*   `matcher`: `Notification`フックでは、通常マッチャーは空（`{}`）にして、すべての通知をキャッチします。特定の通知のみを対象にしたい場合は、`title` や `message` の内容でフィルタリングすることも可能です。
*   `command`: 実行したいシェルスクリプトを指定します。`${title}` と `${message}` という変数が利用可能で、それぞれ通知のタイトルと本文が格納されています。

## 実用例

### 例1: macOSの通知センターに表示する

Claudeからの通知を、macOSのネイティブ通知として表示するフックです。

**`settings.json` に追加する設定例:**

```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": {},
        "command": "osascript -e 'display notification \"${message}\" with title \"Claude Code: ${title}\"'"
      }
    ]
  }
}
```

**説明:**

*   `osascript` は、AppleScriptをコマンドラインから実行するためのmacOSの標準コマンドです。
*   `-e` オプションで、後続の文字列をスクリプトとして実行します。
*   `display notification` コマンドで、指定されたタイトルとメッセージを持つ通知を表示します。

### 例2: Slackに通知を転送する

重要なエラー通知をSlackの特定のチャンネルに投稿するフックです。

**前提:**

*   SlackでIncoming WebhookのURLを取得している必要があります。

**スクリプト (`scripts/notify_slack.sh`):**

```bash
#!/bin/bash

SLACK_WEBHOOK_URL="YOUR_INCOMING_WEBHOOK_URL"
NOTIFICATION_TITLE="$1"
NOTIFICATION_MESSAGE="$2"

# JSONペイロードを作成
JSON_PAYLOAD=$(printf '{\"text\": \"%s: %s\"}' "$NOTIFICATION_TITLE" "$NOTIFICATION_MESSAGE")

# curlコマンドでSlackにPOST
curl -X POST -H 'Content-type: application/json' --data "$JSON_PAYLOAD" "$SLACK_WEBHOOK_URL"
```

**`settings.json`:**

```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": {
          "title": ".*[Ee]rror.*" // タイトルにErrorが含まれる場合のみ
        },
        "command": "scripts/notify_slack.sh \"${title}\" \"${message}\"
      }
    ]
  }
}
```

**説明:**

*   `matcher.title`:正規表現を使い、通知タイトルに `Error` または `error` が含まれる場合にのみフックがトリガーされるようにしています。
*   スクリプトは、受け取ったタイトルとメッセージをJSON形式にまとめ、`curl` を使ってSlackのWebhook URLに送信します。

---

`Notification`フックをカスタマイズすることで、Claude Codeからの情報をより柔軟に、普段のワークフローに統合することができます。
