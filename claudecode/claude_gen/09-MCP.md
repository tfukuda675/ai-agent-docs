# 09. MCP（外部ツール接続）

> **概要**: Model Context Protocol（MCP）で Claude Code CLI を外部ツール・データソースに接続する方法をまとめる。
> **参照元**: <https://code.claude.com/docs/ja/mcp> / <https://code.claude.com/docs/ja/mcp-quickstart>

MCP は AI ツール統合のオープン標準。MCP サーバーは Claude Code にツール・データベース・API へのアクセスを提供する。別ツールからチャットにデータをコピーしているなら、サーバーを接続すると Claude が直接読み書きできる。

> **セキュリティ**: 接続前に各サーバーを信頼できるか確認する。外部コンテンツを取得するサーバーはプロンプトインジェクションのリスクがある。

---

## サーバーの追加

### リモート HTTP サーバー（推奨）

```bash
claude mcp add --transport http <name> <url>

# 例: Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Bearer トークン付き
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer your-token"
```

### リモート SSE サーバー（非推奨、可能なら HTTP を）

```bash
claude mcp add --transport sse <name> <url>
```

### ローカル stdio サーバー

マシン上のローカルプロセスとして実行。`--`（ダブルダッシュ）で Claude 自身のオプションとサーバー実行コマンドを分離する。

```bash
claude mcp add [options] <name> -- <command> [args...]

# 例: Airtable
claude mcp add --env AIRTABLE_API_KEY=YOUR_KEY --transport stdio airtable \
  -- npx -y airtable-mcp-server
```

> `--env` の直後にサーバー名を置かない（別の KEY=value と誤読される）。間に別オプションを挟む。

### JSON から追加

```bash
claude mcp add-json weather-api '{"type":"http","url":"https://api.weather.com/mcp","headers":{"Authorization":"Bearer token"}}'
```

---

## サーバーの管理

```bash
claude mcp list              # 一覧
claude mcp get <name>        # 詳細
claude mcp remove <name>     # 削除
/mcp                         # （セッション内）ステータス確認・認証
```

- HTTP/SSE サーバーは切断時に指数バックオフで自動再接続（最大5回）。stdio は自動再接続しない。
- `claude mcp serve` で Claude Code 自身を MCP サーバーとして起動できる。

---

## インストールスコープ

| スコープ | ロード対象 | 共有 | 保存場所 |
| --- | --- | --- | --- |
| `local`（既定） | 現在のプロジェクトのみ | いいえ | `~/.claude.json` |
| `project` | 現在のプロジェクトのみ | はい（git経由） | プロジェクトルートの `.mcp.json` |
| `user` | すべてのプロジェクト | いいえ | `~/.claude.json` |

`-s` / `--scope` で指定。優先順位: local > project > user > プラグイン > claude.ai コネクタ。

```bash
claude mcp add --transport http paypal --scope project https://mcp.paypal.com/mcp
```

`.mcp.json`（プロジェクトスコープ）はバージョン管理にコミットする設計。セキュリティ上、初回使用時に承認を求められる（`claude mcp reset-project-choices` でリセット）。

### `.mcp.json` の例と環境変数展開

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": { "Authorization": "Bearer ${API_KEY}" }
    }
  }
}
```

`${VAR}` と `${VAR:-default}` を `command` / `args` / `env` / `url` / `headers` で展開できる。

---

## 認証（OAuth）

多くのクラウド MCP サーバーは認証が必要。Claude Code は OAuth 2.0 をサポート。

```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
# セッション内で
/mcp
# → ブラウザでログイン
```

### コマンドラインから認証（v2.1.186+）

```bash
claude mcp login sentry
claude mcp logout sentry
```

- **SSH / ディスプレイなし Linux**では、ブラウザを開けない場合を検出して認可 URL を出力する。ローカルで URL を開き、リダイレクト URL をプロンプトに貼り付ける。`ssh -t` で接続すること。
- ローカルブラウザ検出時でも URL プロンプトを強制するには `--no-browser`。

固定コールバックポートが必要なサーバーは `--callback-port 8080`、事前登録 OAuth 認証情報は `--client-id` / `--client-secret` を使う。

---

## MCP リソース・プロンプトの利用

### リソース参照（`@`）

```text
Can you analyze @github:issue://123 and suggest a fix?
Compare @postgres:schema://users with @docs:file://database/user-model
```

`@server:protocol://resource/path` 形式。`@` を入力すると接続サーバーのリソースが補完に出る。

### プロンプトをコマンドとして（`/`）

```text
/mcp__github__list_prs
/mcp__github__pr_review 456
```

`/mcp__servername__promptname` 形式。引数はスペース区切りで渡す。

---

## 実践例

```bash
# Sentry でエラー監視
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# GitHub（PAT をヘッダーで）
claude mcp add --transport http github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"

# PostgreSQL クエリ
claude mcp add --transport stdio db -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@prod.db.com:5432/analytics"
```

---

## 便利な設定・環境変数

- `MCP_TIMEOUT=10000 claude` — サーバー起動タイムアウト。
- `.mcp.json` エントリの `"timeout": 600000` — ツール実行タイムアウト（ミリ秒）。
- `MAX_MCP_OUTPUT_TOKENS=50000` — MCP 出力トークン上限（既定25,000、10,000超で警告）。
- **ツール検索**（既定有効）: MCP ツールを遅延ロードしコンテキストを節約。`ENABLE_TOOL_SEARCH=false` で無効、`auto:5` でしきい値モード。
- claude.ai で追加したコネクタは、claude.ai 認証時に自動で利用可能。無効化は `disableClaudeAiConnectors: true`。

---

## 関連ドキュメント

- [04-CLIリファレンス.md](04-CLIリファレンス.md) — `claude mcp` コマンド
- [13-権限とセキュリティ.md](13-権限とセキュリティ.md) — MCP ツールの権限
- [06-設定.md](06-設定.md) — MCP 関連環境変数
- [08-よく使うワークフロー.md](08-よく使うワークフロー.md) — MCP の活用
