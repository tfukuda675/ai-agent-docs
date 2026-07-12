# 11. MCP（外部ツール接続）

> **概要**: opencode で Model Context Protocol（MCP）サーバーを設定する方法をまとめる。
> **参照元**: <https://opencode.ai/docs/ja/mcp-servers>

opencode はローカル / リモートの MCP サーバーで LLM に外部ツールを追加できる。`opencode.json` の `mcp` セクションで設定する。

---

## ローカル MCP サーバー（stdio）

マシン上でローカルプロセスとして実行する。

```json
{
  "mcp": {
    "my-local-mcp": {
      "type": "local",
      "command": ["npx", "-y", "my-mcp-command"],
      "enabled": true,
      "environment": { "MY_ENV_VAR": "value" }
    }
  }
}
```

| フィールド | 説明 |
| --- | --- |
| `type` | `"local"` |
| `command` | コマンドと引数の配列 |
| `environment` | 環境変数（任意） |
| `enabled` | 有効/無効 |
| `timeout` | ミリ秒（既定 5000） |

---

## リモート MCP サーバー（HTTP / SSE）

```json
{
  "mcp": {
    "my-remote-mcp": {
      "type": "remote",
      "url": "https://my-mcp-server.com",
      "enabled": true,
      "headers": { "Authorization": "Bearer API_KEY" }
    }
  }
}
```

| フィールド | 説明 |
| --- | --- |
| `type` | `"remote"` |
| `url` | サーバーエンドポイント |
| `headers` | 認証ヘッダー（任意） |
| `oauth` | OAuth 設定オブジェクト、または `false` で自動検出を無効化 |

---

## CLI での管理

```bash
opencode mcp add            # サーバー追加
opencode mcp list           # 一覧・状態（ls）
opencode mcp auth <name>    # OAuth 認証
opencode mcp auth list      # OAuth サーバー一覧
opencode mcp logout <name>  # OAuth 認証情報を削除
opencode mcp debug <name>   # 接続問題のデバッグ
```

---

## OAuth 認証

対応サーバーは動的クライアント登録で自動的に OAuth を処理する。事前設定する場合:

```json
{
  "mcp": {
    "my-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "{env:CLIENT_ID}",
        "clientSecret": "{env:CLIENT_SECRET}",
        "scope": "tools:read tools:execute"
      }
    }
  }
}
```

---

## MCP ツールの制御

グローバル・エージェント単位で glob により有効/無効にできる（[10-権限とツール.md](10-権限とツール.md)）:

```json
{
  "tools": { "my-mcp*": false },
  "agent": { "my-agent": { "tools": { "my-mcp*": true } } }
}
```

---

## 例

- **Sentry**: `"url": "https://mcp.sentry.dev/mcp"`（OAuth）
- **Context7**: `"url": "https://mcp.context7.com/mcp"`
- **GitHub Grep**: `"url": "https://mcp.grep.app"`

---

## 関連ドキュメント

- [04-CLIリファレンス.md](04-CLIリファレンス.md) — `opencode mcp`
- [06-設定.md](06-設定.md) — `mcp` 設定・変数置換
- [10-権限とツール.md](10-権限とツール.md) — MCP ツールの権限
