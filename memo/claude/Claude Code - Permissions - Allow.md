# Claude Code permissions.allow 詳細ガイド

## 1. `permissions.allow` とは？ (Explanation)

`permissions.allow` は、Claude Code が使用できるツールとその操作を明示的に許可（ホワイトリスト化）するための設定です。この設定により、意図しない、あるいは潜在的に危険な操作（例: `rm -rf /` のようなコマンドの実行）を防ぎ、安全な環境で Claude Code を利用することが可能になります。

デフォルトでは、多くの操作が制限されており、実行にはユーザーの許可が必要です。`permissions.allow` を適切に設定することで、この許可プロセスを自動化し、作業効率を大幅に向上させることができます。

## 2. 設定ファイルの場所 (Reference)

`permissions.allow` の設定は、`.claude/settings.json` というファイルに記述します。このファイルは、以下の3つの場所に置くことができ、それぞれ適用の優先順位が異なります。

1.  **ローカルプロジェクト設定:** `[プロジェクト]/.claude/settings.local.json`
    *   個人用の設定です。チームで設定を共有したくない場合や、一時的な設定変更に使用します。`.gitignore` に追加することが推奨されます。
2.  **プロジェクト設定:** `[プロジェクト]/.claude/settings.json`
    *   プロジェクト固有の設定です。チームメンバー間で設定を共有したい場合に使用します。
3.  **ユーザー設定:** `~/.claude/settings.json`
    *   全てのプロジェクトに共通で適用されるグローバルな設定です。

## 3. 基本構文 (Reference)

`settings.json` ファイル内で、`"permissions"` キーの中に `"allow"` というキー名で、許可するツールを配列として記述します。

```json
{
  "permissions": {
    "allow": [
      "ToolName",
      "ToolName(*)",
      "ToolName(filter)"
    ]
  }
}
```

*   `"ToolName"`: ツールの全ての操作を許可します。
*   `"ToolName(*)"`: 任意の引数でのツール使用を許可します。
*   `"ToolName(filter)"`: 特定の条件に一致するツールの呼び出しのみを許可します。フィルタの条件はツールごとに異なります。

## 4. ユースケース別設定例 (How-to)

ここでは、一般的な開発シーンを想定した `permissions.allow` の設定例を紹介します。これらをベースに、ご自身のプロジェクトに合わせてカスタマイズしてください。

### a. ファイル操作

特定のディレクトリ内での自由な読み書きを許可する設定です。

-   `src` ディレクトリ配下の全ファイルの読み取りを許可:
    ```json
    "ReadFile(src/*)"
    ```
-   `src` ディレクトリ配下へのファイル書き込みを許可:
    ```json
    "WriteFile(src/*)"
    ```

### b. シェルコマンドの利用

よく使う安全なコマンドのみを許可し、リスクを低減する設定です。

-   `ls -la` コマンドを許可:
    ```json
    "Bash(ls -la)"
    ```
-   任意の `git` サブコマンド（`status`, `diff` など）を許可:
    ```json
    "Bash(git *)"
    ```
-   **（非推奨）** 全てのシェルコマンドを許可:
    ```json
    "Bash(*)"
    ```

### c. JavaScript/TypeScript開発 (pnpmベース)

`pnpm` を利用したモダンなフロントエンド開発を想定した設定です。

-   **パッケージ管理:**
    ```json
    "Bash(pnpm install)",
    "Bash(pnpm add *)"
    ```

-   **一般的なスクリプト実行:**
    ```json
    "Bash(pnpm dev)",
    "Bash(pnpm build)",
    "Bash(pnpm test)",
    "Bash(pnpm lint)",
    "Bash(pnpm format)"
    ```

-   **CLIツールの実行 (`dlx`経由):**
    ```json
    "Bash(pnpm dlx shadcn-ui@latest add *)",
    "Bash(pnpm dlx degit *)"
    ```

### d. Python開発

`pip` を使ったパッケージ管理を許可する設定です。

-   全ての `pip` コマンドを許可:
    ```json
    "Pip(*)"
    ```
-   特定のパッケージのインストールのみを許可:
    ```json
    "Pip(install fastapi uvicorn)"
    ```

### e. ネットワークアクセス

特定のAPIエンドポイントへのアクセスのみを許可する設定です。

-   `GET` メソッドによる `https://api.example.com/` へのリクエストのみ許可:
    ```json
    "HTTP(GET https://api.example.com/*)"
    ```

## 5. 便利なTips (How-to)

-   **Auto-Acceptモード:** `Shift+Tab` を押すことで、許可プロンプトを都度表示させずに、`allow` で許可された範囲の操作を自動で承認する「Auto-Accept」モードに切り替えることができます。これにより、反復的なタスクを高速化できます。
-   **ワイルドカードの活用:** `*` を使うことで、柔軟な権限設定が可能です。例えば、`ReadFile(src/components/*.js)` のように特定の種類のファイルのみを対象にできます。
-   **`deny` ルールとの組み合わせ:** `allow` で広めの権限を与えつつ、`deny` で特定の危険な操作を禁止することで、柔軟性と安全性を両立できます。例えば、`Bash(*)` を許可しつつ、`Bash(rm *)` を `deny` する、といった設定が考えられます（ただし、`Bash(*)` 自体が非推奨である点には注意が必要です）。

    ```json
    {
      "permissions": {
        "allow": [
          "Bash(*)"
        ],
        "deny": [
          "Bash(rm *)"
        ]
      }
    }
    ```

## 6. セキュリティ上の注意点 (Explanation)

-   **最小権限の原則:** `Bash(*)` や `WriteFile(*)` のように、過度に広範な権限を与えることは避けてください。必要な操作のみを具体的に許可することで、セキュリティリスクを最小限に抑えることができます。
-   **コマンドインジェクションのリスク:** シェルコマンドを許可する場合、意図しない引数が渡されることで、予期せぬコマンドが実行される可能性があります。フィルタをできるだけ厳密に設定することが重要です。
-   **シークレットの扱い:** `permissions.allow` の設定ファイル自体に、APIキーなどのシークレット情報を直接書き込むことは避けてください。

## 7. 実践的なルールセットの例（コピー＆ペースト用）

以下は、`pnpm` を利用する一般的なWeb開発プロジェクト向けの、実用的なルールセットです。これを `.claude/settings.json` または `.claude/settings.local.json` にコピー＆ペーストして、ご自身のプロジェクトに合わせて調整してください。

```json
{
  "permissions": {
    "allow": [
      // ファイル操作: srcディレクトリ内の読み書きを許可
      "ReadFile(src/*)",
      "WriteFile(src/*)",
      "Edit(src/*)",

      // バージョン管理: 一般的なgitコマンドを許可
      "Bash(git *)",

      // パッケージ管理: pnpmを使ったパッケージ操作を許可
      "Bash(pnpm install)",
      "Bash(pnpm add *)",
      "Bash(pnpm up *)",
      "Bash(pnpm remove *)",

      // スクリプト実行: 一般的な開発スクリプトを許可
      "Bash(pnpm dev)",
      "Bash(pnpm build)",
      "Bash(pnpm test)",
      "Bash(pnpm lint)",
      "Bash(pnpm format)",

      // CLIツール: pnpm dlx経由でのツール実行を許可
      "Bash(pnpm dlx *)"
    ],
    "deny": [
      // --- 安全対策: 非常に危険なコマンドを明示的に禁止 ---
      // ファイルシステム・ディスク操作
      "Bash(rm -rf *)",
      "Bash(dd *)",
      "Bash(mkfs *)",

      // 権限・所有権の変更
      "Bash(chmod *)",
      "Bash(chown *)",

      // システム状態の変更
      "Bash(reboot *)",
      "Bash(shutdown *)",

      // 環境の変更
      "Bash(export *)",
      "Bash(alias *)",

      // --- バージョン管理の安全対策 ---
      "Bash(git push --force*)",
      "Bash(git rebase*)",
      "Bash(git reset --hard*)",
      "Bash(git clean -fdx*)",
      "Bash(git branch -D*)"
    ]
  }
}
```