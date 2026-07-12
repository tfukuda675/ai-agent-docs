# 04. CLI リファレンス

> **概要**: `opencode` コマンドとサブコマンド、主要フラグ、非対話実行（`opencode run`）をまとめる。
> **参照元**: <https://opencode.ai/docs/ja/cli>

引数なしの `opencode` は TUI を起動する。サブコマンドで自動化・スクリプト実行ができる。

---

## 主なコマンド

| コマンド | 説明 |
| --- | --- |
| `opencode` | TUI を起動 |
| `opencode run "<prompt>"` | 非対話でプロンプトを実行 |
| `opencode auth login/list/logout` | プロバイダー認証の管理 |
| `opencode agent create/list` | エージェントの作成・一覧 |
| `opencode models [provider]` | モデル一覧（`--refresh` / `--verbose`） |
| `opencode session list` | セッション一覧（`-n`, `--format`） |
| `opencode stats` | トークン使用量・コスト（`--days`, `--models`, `--project`） |
| `opencode export [sessionID]` | セッションを JSON でエクスポート |
| `opencode import <file\|url>` | セッションをインポート |
| `opencode mcp add/list/auth/logout/debug` | MCP サーバー管理（[11-MCP.md](11-MCP.md)） |
| `opencode serve` | ヘッドレス API サーバー（TUI なし） |
| `opencode web` | Web インターフェース付きサーバー |
| `opencode attach <url>` | 実行中サーバーに TUI で接続 |
| `opencode github install/run` | GitHub エージェント（Actions 連携） |
| `opencode acp` | ACP（Agent Client Protocol）サーバーを stdin/stdout で起動 |
| `opencode upgrade [version]` | 更新（`--method`） |
| `opencode uninstall` | アンインストール |

---

## `opencode`（TUI）の主なフラグ

| フラグ | 説明 |
| --- | --- |
| `--continue`, `-c` | 直前のセッションを再開 |
| `--session` | 継続するセッション ID |
| `--prompt` | 使用するプロンプト |
| `--model`, `-m` | `provider/model` 形式でモデル指定 |
| `--agent` | 使用するエージェント |
| `--port` / `--hostname` | リッスンポート / ホスト名 |

---

## `opencode run`（非対話）

TUI を開かずにプロンプトを実行する。CI・スクリプト・パイプ向け。

```bash
opencode run "Explain async/await"
cat error.log | opencode run "このログの異常を教えて"
```

主なフラグ:

| フラグ | 説明 |
| --- | --- |
| `--continue`, `-c` / `--session`, `-s` | セッション再開 / ID 指定 |
| `--fork` | 継続時にセッションをフォーク |
| `--model`, `-m` / `--agent` / `--variant` | モデル / エージェント / バリアント |
| `--file`, `-f` | メッセージにファイルを添付 |
| `--format` | 出力形式（`default` / `json`） |
| `--thinking` | 思考ブロックを表示 |
| `--attach <url>` | 実行中サーバーに接続 |
| `--title` | セッションタイトル |
| `--dir` | 作業ディレクトリ |

サーバーへ接続する例:

```bash
opencode serve
opencode run --attach http://localhost:4096 "Explain async/await"
```

---

## グローバルフラグ

| フラグ | 説明 |
| --- | --- |
| `--help`, `-h` | ヘルプ |
| `--version`, `-v` | バージョン |
| `--print-logs` | ログを stderr に出力 |
| `--log-level` | ログレベル（`DEBUG` / `INFO` / `WARN` / `ERROR`） |

---

## 主な環境変数

| 変数 | 用途 |
| --- | --- |
| `OPENCODE_CONFIG` | 設定ファイルのパス |
| `OPENCODE_CONFIG_DIR` | 設定ディレクトリ |
| `OPENCODE_CONFIG_CONTENT` | インライン設定（実行時上書き） |
| `OPENCODE_SERVER_USERNAME` / `OPENCODE_SERVER_PASSWORD` | serve/web の Basic 認証 |
| `OPENCODE_DISABLE_AUTOUPDATE` | 自動更新を無効化 |
| `OPENCODE_ENABLE_EXPERIMENTAL_MODELS` | 実験的モデルを有効化 |

---

## 関連ドキュメント

- [03-クイックスタート.md](03-クイックスタート.md) — 基本操作
- [05-TUIとキーバインド.md](05-TUIとキーバインド.md) — TUI 操作
- [06-設定.md](06-設定.md) — 設定・環境変数
- [11-MCP.md](11-MCP.md) — `opencode mcp`
