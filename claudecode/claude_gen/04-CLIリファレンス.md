# 04. CLI リファレンス

> **概要**: `claude` コマンドのサブコマンドと主要フラグ、ヘッドレス実行（`-p`）をまとめる。CLI 利用に関係の薄いクラウド/リモート系は簡潔に扱う。
> **参照元**: <https://code.claude.com/docs/ja/cli-reference> / <https://code.claude.com/docs/ja/headless>

> `claude --help` は全フラグを列挙しない。ここに載っていて `--help` に無いフラグも利用できる。

---

## CLI コマンド（サブコマンド）

| コマンド | 説明 | 例 |
| --- | --- | --- |
| `claude` | インタラクティブセッションを開始 | `claude` |
| `claude "query"` | 初期プロンプト付きで開始 | `claude "explain this project"` |
| `claude -p "query"` | クエリを実行して終了（ヘッドレス） | `claude -p "explain this function"` |
| `cat file \| claude -p "query"` | パイプ入力を処理 | `cat logs.txt \| claude -p "explain"` |
| `claude -c` | 現在のディレクトリで最新の会話を続行 | `claude -c` |
| `claude -c -p "query"` | 続行しつつヘッドレス実行 | `claude -c -p "Check for type errors"` |
| `claude -r "<session>" "query"` | セッション ID / 名前で再開 | `claude -r "auth-refactor" "Finish this PR"` |
| `claude update` | 最新バージョンに更新 | `claude update` |
| `claude install [version]` | ネイティブバイナリを（再）インストール | `claude install stable` |
| `claude auth login/logout/status` | サインイン / ログアウト / 状態表示 | `claude auth status` |
| `claude doctor` | インストール/設定の読み取り専用診断 | `claude doctor` |
| `claude setup-token` | CI 向け長期 OAuth トークンを生成 | `claude setup-token` |
| `claude mcp` | MCP サーバーを設定（[09-MCP.md](09-MCP.md)） | `claude mcp` |
| `claude mcp login/logout <name>` | MCP サーバーの OAuth ログイン/ログアウト（v2.1.186+） | `claude mcp login sentry` |
| `claude plugin`（別名 `plugins`） | プラグイン管理 | `claude plugin install ...` |
| `claude config` | 設定の読み書き（[06-設定.md](06-設定.md)） | `claude config` |
| `claude project purge [path]` | プロジェクトのローカル状態を削除 | `claude project purge ~/work/repo --dry-run` |

> クラウド/リモート/バックグラウンド系（`claude gateway` / `agents` / `attach` / `logs` / `respawn` / `stop` / `rm` / `remote-control` / `ultrareview` など）はスコープ外だが存在する。詳細は公式 CLI リファレンスを参照。

サブコマンドを誤入力すると最も近い一致を提案して終了する（例: `claude udpate` → `Did you mean claude update?`）。

---

## 主要な CLI フラグ（CLI 利用でよく使うもの）

### セッション・会話

| フラグ | 説明 |
| --- | --- |
| `--continue`, `-c` | 現在のディレクトリで最新の会話を読み込む |
| `--resume`, `-r` | ID/名前で再開、または対話ピッカー表示 |
| `--fork-session` | 再開時に新しいセッション ID を作る |
| `--session-id <uuid>` | 特定のセッション ID を使用 |
| `--name`, `-n <name>` | セッション表示名を設定 |
| `--from-pr <n>` | PR にリンクされたセッションを再開 |

### モデル・努力レベル

| フラグ | 説明 |
| --- | --- |
| `--model <alias\|id>` | モデルを設定（`sonnet` / `opus` / `haiku` / `fable` またはフル ID） |
| `--fallback-model <list>` | プライマリ過負荷時のフォールバック（カンマ区切り） |
| `--effort <level>` | 努力レベル: `low` / `medium` / `high` / `xhigh` / `max` / `ultracode` |

### 権限・ツール

| フラグ | 説明 |
| --- | --- |
| `--permission-mode <mode>` | 開始時の権限モード（`default` / `acceptEdits` / `plan` / `auto` / `dontAsk` / `bypassPermissions` / `manual`） |
| `--allowedTools`, `--allowed-tools` | 許可（権限を求めず実行）するツール |
| `--disallowedTools`, `--disallowed-tools` | 拒否ルール（ベア名でモデルから削除、スコープ付きは呼び出しのみ拒否） |
| `--tools <list>` | 使用可能な組み込みツールを制限（`""`=全無効 / `"default"`=全有効 / `"Bash,Edit,Read"`） |
| `--dangerously-skip-permissions` | 全権限プロンプトをスキップ（= `bypassPermissions`） |
| `--allow-dangerously-skip-permissions` | `Shift+Tab` サイクルに `bypassPermissions` を追加（開始時は有効化しない） |
| `--add-dir <paths>` | 追加の作業ディレクトリ（ファイルアクセスを許可） |

### 設定・システムプロンプト

| フラグ | 説明 |
| --- | --- |
| `--settings <path\|json>` | 設定 JSON を上書き適用 |
| `--setting-sources <list>` | 読み込む設定ソース（`user,project,local`） |
| `--mcp-config <path>` | JSON から MCP サーバーを読み込む |
| `--strict-mcp-config` | `--mcp-config` のみを使い他の MCP 設定を無視 |
| `--system-prompt <text>` | デフォルトシステムプロンプトを置換 |
| `--system-prompt-file <path>` | ファイルでシステムプロンプトを置換 |
| `--append-system-prompt <text>` | デフォルトに追加 |
| `--append-system-prompt-file <path>` | ファイル内容をデフォルトに追加 |

> `--system-prompt` と `--system-prompt-file` は排他。追加(`--append-*`)は置換フラグと併用可。挙動をタスクに合わせて選ぶ。プロジェクト規約は CLAUDE.md、永続ペルソナは出力スタイルを使う。

### ヘッドレス / スクリプト（`-p` 系）

| フラグ | 説明 |
| --- | --- |
| `--print`, `-p` | インタラクティブなしで応答を出力 |
| `--output-format <fmt>` | `text` / `json` / `stream-json` |
| `--input-format <fmt>` | `text` / `stream-json` |
| `--max-turns <n>` | エージェントのターン数を制限（プリントモード） |
| `--max-budget-usd <n>` | API 支出上限（プリントモード） |
| `--include-partial-messages` | 部分ストリーミングイベントを含める（`-p` + `stream-json`） |
| `--verbose` | 詳細ログ・ターンごとの完全出力 |
| `--bare` | 最小モード（hooks/skills/plugins/MCP/自動メモリ/CLAUDE.md 検出をスキップ、起動が速い） |
| `--no-session-persistence` | セッションをディスクに保存しない（プリントモード） |

### 診断・その他

| フラグ | 説明 |
| --- | --- |
| `--version`, `-v` | バージョン番号を出力 |
| `--debug [cats]` | デバッグモード（例: `"api,mcp"`、`"!statsig"`） |
| `--debug-file <path>` | デバッグログをファイルに書き出す |
| `--safe-mode` | 全カスタマイズ無効で起動（設定トラブル切り分け用） |
| `--disable-slash-commands` | 全スキル/コマンドを無効化 |
| `--ide` | 有効な IDE が1つなら自動接続 |
| `--worktree`, `-w [name]` | 分離した git worktree で開始 |

---

## ヘッドレス実行の例（パイプ / 自動化）

```bash
# ログを解析
tail -200 app.log | claude -p "Slack me if you see any anomalies"

# 変更ファイルをまとめてレビュー
git diff main --name-only | claude -p "review these changed files for security issues"

# JSON で結果を取得
claude -p "query" --output-format json
```

---

## 関連ドキュメント

- [05-インタラクティブモード.md](05-インタラクティブモード.md) — 対話操作・ショートカット
- [06-設定.md](06-設定.md) — `settings.json`・環境変数・モデル設定
- [13-権限とセキュリティ.md](13-権限とセキュリティ.md) — 権限モードとツール許可
- [09-MCP.md](09-MCP.md) — MCP サーバー
