# OpenCode 公式ドキュメント整理版（Linux）

確認日: 2026-07-12  
対象: LinuxにOpenCodeをインストール済みの利用者

この文書は、OpenCode公式ドキュメントからLinuxのターミナル利用に必要な内容を抽出し、Markdownへ再構成したものです。インストール手順、macOS・Windows固有の説明、Desktop・IDE中心の説明、重複する例、ページ内ナビゲーションは省略しています。

OpenCodeは更新されるため、利用中のバージョンではコマンド、設定項目、既定値が異なる場合があります。

## 1. 概要

OpenCodeは、ターミナル、デスクトップ、IDEで利用できるオープンソースのAIコーディングエージェントです。複数のLLMプロバイダーとモデルに対応し、コードの調査、編集、コマンド実行、開発ツールとの連携を行います。

主な機能:

- コードベースの調査、説明、編集
- シェルコマンド、テスト、フォーマッタ、LSPの利用
- 複数のLLMプロバイダーとモデルの切り替え
- Build、Planおよびカスタムエージェント
- `AGENTS.md`によるプロジェクト指示
- Skills、カスタムコマンド、カスタムツール
- MCPサーバー、プラグインによる拡張
- セッションの継続、分岐、共有、入出力

出典: [はじめに](https://opencode.ai/docs/ja/)

## 2. 起動とプロバイダー認証

### 状態確認

```bash
opencode --version
opencode --help
```

### プロバイダーへのログイン

```bash
opencode auth login
opencode auth list
opencode auth logout
```

- `auth login`: 利用するプロバイダーの認証情報を設定
- `auth list` / `auth ls`: 認証済みプロバイダーを表示
- `auth logout`: 保存されたプロバイダー認証情報を削除

認証情報は `~/.local/share/opencode/auth.json` に保存されます。OpenCodeは認証情報ファイルに加え、環境変数とプロジェクトの `.env` も確認します。秘密情報を `opencode.json` やリポジトリへ直接記載しないでください。

利用可能なモデルは次のコマンドで確認できます。

```bash
opencode models
opencode models anthropic
opencode models --refresh
```

モデル名は `provider/model` 形式です。

出典: [CLI](https://opencode.ai/docs/ja/cli/)、[プロバイダー](https://opencode.ai/docs/ja/providers/)

## 3. TUIの開始と基本操作

引数なしで実行すると、現在のディレクトリでTUIが起動します。プロジェクトパスも指定できます。

```bash
cd /path/to/project
opencode

opencode /path/to/project
```

主な起動オプション:

| オプション | 内容 |
|---|---|
| `-c`, `--continue` | 直前のセッションを継続 |
| `--session <id>` | 指定したセッションを継続 |
| `--fork` | 継続するセッションから分岐 |
| `--prompt <text>` | 最初のプロンプトを指定 |
| `-m`, `--model <provider/model>` | 使用モデルを指定 |
| `--agent <name>` | 使用エージェントを指定 |

質問の例:

```text
このプロジェクトの構成と主要な処理を説明してください。
認証処理はどこに実装されていますか？
```

変更の例:

```text
認証エラーを再現するテストを追加し、原因を修正してください。
関連するテストとlintを実行してください。
```

出典: [はじめに](https://opencode.ai/docs/ja/)、[CLI](https://opencode.ai/docs/ja/cli/)

## 4. CLIリファレンス

| コマンド | 内容 |
|---|---|
| `opencode` | TUIを開始 |
| `opencode run "message"` | TUIを開かず非対話で実行 |
| `opencode agent create` | カスタムエージェントを作成 |
| `opencode agent list` | 利用可能なエージェントを表示 |
| `opencode auth login` | プロバイダーへログイン |
| `opencode auth list` | 認証済みプロバイダーを表示 |
| `opencode models [provider]` | 利用可能なモデルを表示 |
| `opencode mcp add` | MCPサーバーを追加 |
| `opencode mcp list` | MCPサーバーと接続状態を表示 |
| `opencode session list` | セッション一覧を表示 |
| `opencode stats` | トークン使用量とコスト統計を表示 |
| `opencode export [sessionID]` | セッションをJSONとして出力 |
| `opencode import <file>` | JSONまたは共有URLからセッションを入力 |
| `opencode serve` | ヘッドレスHTTPサーバーを開始 |
| `opencode attach [url]` | 実行中のOpenCodeサーバーへTUIを接続 |
| `opencode upgrade` | OpenCodeを更新 |

### 非対話実行

```bash
opencode run "このプロジェクトのエントリーポイントを説明してください"
opencode run -m anthropic/model-name "テスト失敗を調査してください"
opencode run -f error.log "このログを分析してください"
```

`opencode run`の主なオプション:

| オプション | 内容 |
|---|---|
| `-c`, `--continue` | 直前のセッションを継続 |
| `-s`, `--session` | セッションIDを指定 |
| `--fork` | 継続元からセッションを分岐 |
| `-m`, `--model` | モデルを指定 |
| `--agent` | エージェントを指定 |
| `-f`, `--file` | ファイルをメッセージへ添付 |
| `--format` | 出力形式を指定。既定形式またはJSON |
| `--title` | セッションタイトルを指定 |
| `--dir` | 実行ディレクトリを指定 |

グローバルオプション:

| オプション | 内容 |
|---|---|
| `-h`, `--help` | ヘルプを表示 |
| `-v`, `--version` | バージョンを表示 |
| `--print-logs` | ログを標準エラーへ出力 |
| `--log-level` | `DEBUG`、`INFO`、`WARN`、`ERROR`を指定 |

出典: [CLI](https://opencode.ai/docs/ja/cli/)

## 5. TUIコマンドとキーバインド

TUIでは `/` からコマンドを実行できます。主な組み込みコマンド:

| コマンド | 内容 |
|---|---|
| `/init` | プロジェクトを解析して `AGENTS.md` を作成・更新 |
| `/models` | モデルを選択 |
| `/agents` | エージェントを選択 |
| `/new` | 新しいセッションを開始 |
| `/sessions` | セッションを一覧・切り替え |
| `/compact` | 現在のセッションを圧縮 |
| `/undo` | 直前のユーザーメッセージと変更を取り消す |
| `/redo` | 取り消した操作を再適用 |
| `/share` | 現在のセッションを共有 |
| `/unshare` | セッション共有を解除 |
| `/export` | 会話をエディターで開いて保存 |
| `/help` | ヘルプを表示 |
| `/exit` | OpenCodeを終了 |

実際に利用できるコマンドはTUIのコマンド一覧で確認します。カスタムコマンドも追加できます。

OpenCodeはリーダーキー方式を使います。既定のリーダーキーは `Ctrl+X` で、たとえば新規セッションはリーダーキーに続けて `n` を入力します。キーバインドは `tui.json` で変更できます。

出典: [コマンド](https://opencode.ai/docs/ja/commands/)、[キーバインド](https://opencode.ai/docs/ja/keybinds/)、[TUI](https://opencode.ai/docs/ja/tui/)

## 6. AGENTS.mdとルール

OpenCodeは `AGENTS.md` の指示をLLMのコンテキストへ追加します。

| 場所 | 用途 |
|---|---|
| `AGENTS.md` | プロジェクト固有の共有ルール |
| `~/.config/opencode/AGENTS.md` | ユーザーの全プロジェクト共通ルール |
| `CLAUDE.md` | プロジェクトに `AGENTS.md` がない場合のClaude Code互換フォールバック |
| `~/.claude/CLAUDE.md` | OpenCodeグローバルルールがない場合のフォールバック |

`/init`はリポジトリを調査し、ビルド・lint・テスト方法、構造、規約などを含む `AGENTS.md` を作成または更新します。プロジェクト用 `AGENTS.md` はGitで共有できます。

読み込みの優先関係:

1. 現在位置から上位へ探索したローカルの `AGENTS.md`。存在しなければ `CLAUDE.md`
2. `~/.config/opencode/AGENTS.md`
3. 上記がなければ `~/.claude/CLAUDE.md`

追加の指示ファイルは `opencode.json` で指定できます。

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "CONTRIBUTING.md",
    "docs/guidelines.md",
    ".cursor/rules/*.md"
  ]
}
```

`instructions`にはリモートURLも指定できます。指定すると起動時に外部通信が発生するため、信頼できるURLだけを使用します。

出典: [ルール](https://opencode.ai/docs/ja/rules/)

## 7. 設定

OpenCodeはJSONまたはJSONC形式の設定を読み込みます。

主な場所:

| 場所 | 用途 |
|---|---|
| `~/.config/opencode/opencode.json` | ユーザー共通設定 |
| プロジェクト内の `opencode.json` | プロジェクト設定 |
| `.opencode/` | エージェント、コマンド、Skills、プラグインなど |
| `~/.config/opencode/tui.json` | TUI設定 |

設定ファイルは置き換えではなくマージされ、後から読み込まれる設定が競合するキーを上書きします。カスタム設定ファイルは `OPENCODE_CONFIG`、設定ディレクトリは `OPENCODE_CONFIG_DIR` で指定できます。

最小例:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "provider/model-name",
  "permission": {
    "*": "ask",
    "read": "allow",
    "glob": "allow",
    "grep": "allow",
    "edit": "ask",
    "bash": "ask",
    "webfetch": "ask",
    "websearch": "ask",
    "external_directory": "ask"
  }
}
```

TUIは別の `tui.json` で設定します。

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "opencode",
  "diff_style": "auto",
  "mouse": true,
  "keybinds": {
    "leader": "ctrl+x"
  }
}
```

出典: [設定](https://opencode.ai/docs/ja/config/)、[TUI](https://opencode.ai/docs/ja/tui/)

## 8. 権限

OpenCodeの権限は次の3種類です。

| 値 | 動作 |
|---|---|
| `allow` | 確認なしで実行 |
| `ask` | 実行前に承認を要求 |
| `deny` | 実行を禁止 |

重要: 公式ドキュメントによると、未設定の場合は大半の権限が `allow` です。`external_directory` と `doom_loop` は `ask`、`read`は通常 `allow` ですが、`.env`ファイルには既定の拒否ルールがあります。

主な権限キー:

| キー | 対象 |
|---|---|
| `read` | ファイル読み取り |
| `edit` | `edit`、`write`、`apply_patch`によるファイル変更 |
| `glob`、`grep`、`list` | ファイル・内容検索 |
| `bash` | シェルコマンド |
| `task` | サブエージェント起動 |
| `skill` | Skillの読み込み |
| `lsp` | LSP問い合わせ |
| `webfetch`、`websearch` | 外部取得と検索 |
| `external_directory` | 作業ディレクトリ外へのアクセス |
| `doom_loop` | 同じツール呼び出しが繰り返された場合の回復確認 |

詳細なルールではワイルドカードを使用できます。複数のルールに一致した場合、最後に一致したルールが優先されます。

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": {
      "*": "ask",
      "git status *": "allow",
      "git diff *": "allow",
      "git commit *": "ask",
      "git push *": "deny",
      "rm *": "deny"
    },
    "edit": "ask",
    "webfetch": "ask",
    "websearch": "ask",
    "external_directory": "ask"
  }
}
```

作業ディレクトリ外のパスを許可する場合は `external_directory` を明示します。許可した外部ディレクトリには通常のツール権限も適用されるため、必要なら `read` や `edit` に追加の制限を設定します。

承認画面では、今回だけ許可、セッション中の同種操作を許可、拒否を選択できます。

出典: [権限](https://opencode.ai/docs/ja/permissions/)、[ツール](https://opencode.ai/docs/ja/tools/)

## 9. エージェント

エージェントは、プロンプト、モデル、ツール、権限を用途別に設定したものです。

主な組み込みエージェント:

| エージェント | 内容 |
|---|---|
| Build | コード変更とツール利用を行う標準エージェント |
| Plan | 調査と計画向け。既定では変更操作が制限される |

エージェントは主エージェントとサブエージェントに分かれます。サブエージェントはタスクツールまたは `@agent-name` で呼び出せます。

カスタムエージェント:

```bash
opencode agent create
opencode agent list
```

設定は `opencode.json` またはMarkdownファイルに定義できます。

| 場所 | 用途 |
|---|---|
| `~/.config/opencode/agents/*.md` | ユーザー共通エージェント |
| `.opencode/agents/*.md` | プロジェクト用エージェント |

読み取り専用レビューエージェントの例:

```markdown
---
description: コードを変更せずにレビューする
mode: subagent
permission:
  edit: deny
  bash:
    "*": ask
    "git diff *": allow
  webfetch: deny
  websearch: deny
---

コードを変更せず、バグ、回帰、セキュリティ上の問題を報告する。
```

出典: [エージェント](https://opencode.ai/docs/ja/agents/)

## 10. Skills、コマンド、ツール、MCP

### Skills

Skillsは、必要なときに読み込まれる再利用可能な `SKILL.md` です。

主な配置場所:

- `.opencode/skills/<name>/SKILL.md`
- `~/.config/opencode/skills/<name>/SKILL.md`
- `.agents/skills/<name>/SKILL.md`
- `~/.agents/skills/<name>/SKILL.md`
- Claude Code互換の `.claude/skills/<name>/SKILL.md`

`SKILL.md`には `name` と `description` を含むYAMLフロントマターが必要です。Skillの利用は `permission.skill` で制御できます。

### カスタムコマンド

繰り返し使うプロンプトをコマンドとして定義できます。

| 場所 | 用途 |
|---|---|
| `.opencode/commands/*.md` | プロジェクト用 |
| `~/.config/opencode/commands/*.md` | ユーザー共通 |

### 組み込みツール

主なツールには `bash`、`read`、`edit`、`write`、`glob`、`grep`、`list`、`lsp`、`task`、`skill`、`webfetch`、`websearch`、`todowrite`があります。ツールの有効範囲は権限で制御します。

### MCP

```bash
opencode mcp add
opencode mcp list
opencode mcp auth [name]
opencode mcp logout [name]
opencode mcp debug <name>
```

MCPサーバーはローカルまたはリモートとして設定できます。MCPツールにも `permission` のワイルドカードルールを適用できます。

```json
{
  "permission": {
    "mymcp_*": "ask"
  }
}
```

出典: [Skills](https://opencode.ai/docs/ja/skills/)、[コマンド](https://opencode.ai/docs/ja/commands/)、[ツール](https://opencode.ai/docs/ja/tools/)、[MCPサーバー](https://opencode.ai/docs/ja/mcp-servers/)

## 11. セッション管理

```bash
opencode session list
opencode session list --format json
opencode stats
opencode export [sessionID]
opencode import session.json
```

- セッションは継続またはフォークできます。
- `stats`はトークン使用量とコスト統計を表示します。
- `export`はセッションをJSONで出力します。
- `import`はJSONファイルまたは共有URLを入力します。
- セッション共有は外部へ内容を公開するため、機密情報が含まれていないことを確認します。

出典: [CLI](https://opencode.ai/docs/ja/cli/)、[共有](https://opencode.ai/docs/ja/share/)

## 12. トラブルシューティング

### ログと設定の確認

```bash
opencode --print-logs
opencode --log-level DEBUG
opencode auth list
opencode models
opencode mcp list
```

ログは通常 `~/.local/share/opencode/log/` に保存されます。設定問題では、グローバル設定、プロジェクト設定、環境変数による上書きを確認します。

### プロバイダーまたはモデルを利用できない

- `opencode auth list`で認証済みプロバイダーを確認する。
- 必要な環境変数が現在のシェルで利用可能か確認する。値自体はログへ出力しない。
- `opencode models [provider]`で正確なモデルIDを確認する。
- `opencode models --refresh`でモデル一覧を更新する。
- プロバイダー固有の設定を確認する。

### MCPへ接続できない

```bash
opencode mcp list
opencode mcp auth <name>
opencode mcp debug <name>
```

ローカルMCPではコマンド、引数、環境変数を確認します。リモートMCPではURL、OAuth状態、ネットワーク設定を確認します。

### AGENTS.mdまたはSkillが読み込まれない

- `AGENTS.md`の配置と優先関係を確認する。
- `AGENTS.md`がある場合、同じ階層の `CLAUDE.md`はフォールバックとして使われない。
- `SKILL.md`のファイル名を大文字にする。
- Skillの `name` と `description` を確認する。
- Skill名とディレクトリ名を一致させる。
- `permission.skill`で拒否されていないか確認する。

### TUI表示に問題がある

- 対応する最新のターミナルエミュレーターを使用する。
- `tui.json`の構文と設定値を確認する。
- 問題のあるカスタムテーマやキーバインドを一時的に外す。
- `--print-logs --log-level DEBUG`でエラーを確認する。

出典: [トラブルシューティング](https://opencode.ai/docs/ja/troubleshooting/)、[設定](https://opencode.ai/docs/ja/config/)、[Skills](https://opencode.ai/docs/ja/skills/)

## 13. 参照した公式ドキュメント

- [はじめに](https://opencode.ai/docs/ja/)
- [CLI](https://opencode.ai/docs/ja/cli/)
- [TUI](https://opencode.ai/docs/ja/tui/)
- [設定](https://opencode.ai/docs/ja/config/)
- [プロバイダー](https://opencode.ai/docs/ja/providers/)
- [ルール](https://opencode.ai/docs/ja/rules/)
- [権限](https://opencode.ai/docs/ja/permissions/)
- [ツール](https://opencode.ai/docs/ja/tools/)
- [エージェント](https://opencode.ai/docs/ja/agents/)
- [Skills](https://opencode.ai/docs/ja/skills/)
- [コマンド](https://opencode.ai/docs/ja/commands/)
- [キーバインド](https://opencode.ai/docs/ja/keybinds/)
- [MCPサーバー](https://opencode.ai/docs/ja/mcp-servers/)
- [共有](https://opencode.ai/docs/ja/share/)
- [トラブルシューティング](https://opencode.ai/docs/ja/troubleshooting/)

