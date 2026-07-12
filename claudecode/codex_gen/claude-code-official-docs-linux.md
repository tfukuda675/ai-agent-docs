# Claude Code 公式ドキュメント整理版（Linux）

確認日: 2026-07-12  
対象: LinuxにClaude Codeをインストール済みの利用者

この文書は、Claude Code公式ドキュメントからLinuxのCLI利用に必要な内容を抽出し、Markdownへ再構成したものです。インストール手順、macOS・Windows固有の説明、Web・Desktop版中心の説明、重複する例、ページ内ナビゲーションは省略しています。

Claude Codeは更新されるため、CLIのバージョン、契約プラン、組織設定によってコマンドや動作が異なる場合があります。

## 1. 概要

Claude Codeは、コードベースの読み取り、ファイル編集、コマンド実行、開発ツールとの連携を行うエージェント型コーディングツールです。

主な用途:

- コードベースの調査と説明
- 機能実装、バグ修正、リファクタリング
- テスト作成と実行
- lintエラーや型エラーの修正
- Git操作、コミット、Pull Request作成の支援
- MCPによる外部ツール連携
- `CLAUDE.md`、Skills、Hooksによる動作のカスタマイズ

出典: [概要](https://code.claude.com/docs/ja/overview)

## 2. 起動と認証

### 状態確認

```bash
claude --version
claude doctor
```

- `claude --version`: バージョンを表示
- `claude doctor`: インストール、設定、更新状態などを診断

### セッション開始

```bash
cd /path/to/project
claude
```

初回利用時は画面の案内に従って認証します。認証後は次のセッション内コマンドを利用できます。

| コマンド | 内容 |
|---|---|
| `/login` | ログインまたはアカウントの切り替え |
| `/logout` | ログアウト |
| `/status` | バージョン、モデル、アカウント、設定状態を表示 |

出典: [クイックスタート](https://code.claude.com/docs/ja/quickstart)、[高度なセットアップ](https://code.claude.com/docs/ja/setup)

## 3. 基本操作

Claude Codeは、依頼に応じてプロジェクト内のファイルを検索し、必要なファイルを読み取ります。

調査の例:

```text
このプロジェクトの目的を説明してください。
使用している技術を特定してください。
メインエントリーポイントはどこですか？
ディレクトリ構造を説明してください。
```

変更の例:

```text
認証モジュールにテストを追加し、テストを実行してください。
このエラーの原因を調査し、修正してください。
lintエラーを修正し、すべてのチェックを再実行してください。
```

ファイル変更やコマンド実行では、権限設定に応じて実行前の確認が表示されます。

出典: [クイックスタート](https://code.claude.com/docs/ja/quickstart)

## 4. CLIリファレンス

### 主な起動コマンド

| コマンド | 内容 |
|---|---|
| `claude` | 対話セッションを開始 |
| `claude "query"` | 最初のプロンプトを指定して開始 |
| `claude -p "query"` | 非対話モードで実行し、結果を出力して終了 |
| `cat file \| claude -p "query"` | 標準入力の内容を処理 |
| `claude -c` | 現在のディレクトリで直近の会話を継続 |
| `claude -r` | 再開するセッションを選択 |
| `claude -r "session" "query"` | セッションを指定して再開 |
| `claude update` | Claude Codeを更新 |
| `claude doctor` | インストールと設定を診断 |
| `claude --help` | CLIヘルプを表示 |

### 主なオプション

| オプション | 内容 |
|---|---|
| `-p`, `--print` | 非対話モードで応答を出力 |
| `-c`, `--continue` | 直近の会話を継続 |
| `-r`, `--resume` | 指定または選択した会話を再開 |
| `--model` | 使用モデルを指定 |
| `--permission-mode` | 開始時の権限モードを指定 |
| `--allowedTools` | 確認なしで利用できるツールを指定 |
| `--disallowedTools` | 利用を禁止するツールを指定 |
| `--tools` | Claudeが利用可能なツールを制限 |
| `--add-dir` | 作業対象の追加ディレクトリを指定 |
| `--append-system-prompt` | 既定のシステムプロンプトへ指示を追加 |
| `--append-system-prompt-file` | ファイルから追加指示を読み込む |
| `--input-format` | 非対話モードの入力形式を指定 |
| `--output-format` | 非対話モードの出力形式を指定 |
| `--max-turns` | 非対話モードの最大ターン数を指定 |
| `--verbose` | 詳細ログを表示 |
| `--debug` | デバッグモードを有効化 |

正確な引数は、インストールされているCLIの `claude --help` で確認します。

出典: [CLIリファレンス](https://code.claude.com/docs/ja/cli-reference)

## 5. セッション内コマンド

メッセージの先頭で `/` に続けて入力します。`/`だけを入力すると、利用可能なコマンドを検索できます。

| コマンド | 内容 |
|---|---|
| `/help` | ヘルプを表示 |
| `/status` | セッション、アカウント、モデル、設定の状態を表示 |
| `/config` | 設定画面を開く |
| `/permissions` | 権限ルールを表示・管理 |
| `/model` | 使用モデルを表示・変更 |
| `/init` | プロジェクト用の `CLAUDE.md` を作成 |
| `/memory` | 指示ファイルと自動メモリを管理 |
| `/plan` | Plan Modeへ切り替え |
| `/context` | コンテキスト使用状況を表示 |
| `/compact` | 会話を要約してコンテキストを圧縮 |
| `/clear` | 会話履歴を消去して新しい会話を開始 |
| `/resume` | 過去のセッションを選択して再開 |
| `/rename` | 現在のセッションに名前を付ける |
| `/mcp` | MCPサーバーと認証を管理 |
| `/hooks` | Hooks設定を表示・管理 |
| `/agents` | サブエージェント設定を管理 |
| `/tasks` | バックグラウンドタスクを表示 |
| `/exit` | Claude Codeを終了 |

提供状況はOS、CLIバージョン、プラン、組織設定によって異なります。

出典: [コマンド](https://code.claude.com/docs/ja/commands)

## 6. メモリと指示ファイル

Claude Codeは `CLAUDE.md` を読み込み、プロジェクトの規約や作業方法をセッションへ反映します。

| 場所 | 用途 |
|---|---|
| `~/.claude/CLAUDE.md` | すべてのプロジェクトに適用するユーザー指示 |
| `CLAUDE.md` | リポジトリで共有するプロジェクト指示 |
| `.claude/CLAUDE.md` | プロジェクト指示の別配置 |
| `CLAUDE.local.md` | リポジトリで共有しない個人用指示 |
| `.claude/rules/*.md` | 指示を複数ファイルや対象パス別に分割 |

`/init`で初期ファイルを作成できます。`/memory`では、読み込まれている指示ファイルと自動メモリを確認できます。

指示作成上の要点:

- コマンド、規約、制約を具体的に書く。
- 曖昧または矛盾する指示を避ける。
- 常に必要でない情報は対象パス別のrulesへ分ける。
- ファイルを長くしすぎない。公式では200行を超える場合に整理を推奨している。
- 決まったタイミングで必ず実行する処理にはHooksを使う。

ルートの `CLAUDE.md` はコンテキスト圧縮後に再度読み込まれます。サブディレクトリの指示ファイルは、そのディレクトリ内のファイルを再び扱うときに読み込まれます。

出典: [メモリ](https://code.claude.com/docs/ja/memory)

## 7. 設定

| 設定ファイル | 適用範囲 | 共有 |
|---|---|---|
| `~/.claude/settings.json` | ユーザーの全プロジェクト | 通常は共有しない |
| `.claude/settings.json` | 現在のプロジェクト | Gitで共有可能 |
| `.claude/settings.local.json` | 現在のプロジェクトの個人設定 | Gitで共有しない |

OAuthセッション、ユーザー・ローカルスコープのMCP設定、プロジェクト状態などは `~/.claude.json` に保存されます。プロジェクトスコープのMCP設定は `.mcp.json` に保存されます。

設定例:

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "permissions": {
    "allow": [
      "Bash(npm run lint)",
      "Bash(npm run test *)"
    ],
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)"
    ]
  }
}
```

`$schema`を指定すると、対応エディターで補完と検証を利用できます。設定は `/config` からも変更でき、読み込まれた設定ソースは `/status` で確認できます。

出典: [設定](https://code.claude.com/docs/ja/settings)

## 8. 権限

| ルール | 動作 |
|---|---|
| `allow` | 手動確認なしで利用を許可 |
| `ask` | 利用するたびに確認 |
| `deny` | 利用を禁止 |

ルールは `deny`、`ask`、`allow` の順で評価され、最初に一致したルールが適用されます。

| 権限モード | 内容 |
|---|---|
| `default` | 標準モード。必要な操作で確認 |
| `acceptEdits` | 作業ディレクトリ内の編集と一部ファイル操作を自動承認 |
| `plan` | 読み取りと調査を中心に行い、ソースファイルを編集しない |
| `dontAsk` | 明示的に許可された操作以外は確認せず拒否 |
| `bypassPermissions` | 権限確認を省略。隔離環境以外では使用しない |

`CLAUDE.md`は指示であり、アクセス制御ではありません。機密ファイルへのアクセスを防ぐ場合は `permissions.deny` を使います。

```json
{
  "permissions": {
    "deny": [
      "Read(./.env)",
      "Read(./.env.*)",
      "Read(./secrets/**)",
      "Read(./config/credentials.json)"
    ]
  }
}
```

権限は `/permissions` から確認・変更できます。

出典: [権限](https://code.claude.com/docs/ja/permissions)

## 9. Skills、Hooks、MCP

### Skills

Skillsは、繰り返し利用する指示やワークフローをMarkdownファイルとして定義する機能です。スラッシュコマンドとして呼び出すか、内容に応じてClaudeが利用します。

### Hooks

Hooksはライフサイクルイベントで処理を実行します。編集後のフォーマット、コマンドの検査、通知、検証などに利用できます。

主なイベント:

- `SessionStart`: セッション開始・再開時
- `UserPromptSubmit`: プロンプトを処理する前
- `PreToolUse`: ツール実行前
- `PermissionRequest`: 権限確認時
- `PostToolUse`: ツール成功後
- `PostToolUseFailure`: ツール失敗後
- `PreCompact` / `PostCompact`: コンテキスト圧縮の前後
- `SessionEnd`: セッション終了時

Hooksは `~/.claude/settings.json`、`.claude/settings.json`、`.claude/settings.local.json` に設定できます。シェルコマンドなどを自動実行できるため、設定内容を確認してから利用します。

### MCP

MCP（Model Context Protocol）は、外部データソースや追加ツールをClaude Codeへ接続する仕組みです。`/mcp`で接続状態とOAuth認証を管理できます。

出典: [Skills](https://code.claude.com/docs/ja/skills)、[Hooks](https://code.claude.com/docs/ja/hooks)、[MCP](https://code.claude.com/docs/ja/mcp)

## 10. トラブルシューティング

### コマンドが見つからない

```bash
command -v claude
echo "$PATH"
```

Linux向けネイティブインストールの標準的な実行ファイル位置は `~/.local/bin/claude` です。`~/.local/bin` が `PATH` に含まれているか確認します。

### ログインをリセットする

1. セッション内で `/logout` を実行する。
2. Claude Codeを終了する。
3. `claude`を起動して再認証する。

ブラウザが開かない場合は、ログイン画面でOAuth URLをコピーしてブラウザで開きます。`ANTHROPIC_API_KEY`が設定されている場合、OAuth認証よりAPIキーが使用されることがあります。利用中の認証方式は `/status` で確認します。

SSH、リモート環境、コンテナでは、ブラウザが別のホストで開き、ローカルコールバックへ接続できない場合があります。この場合は表示されたURLをブラウザへ手動で渡します。

### 設定が反映されない

- `/status`で読み込まれた設定ソースを確認する。
- JSON構文とファイルの配置を確認する。
- `.claude/settings.local.json`による上書きを確認する。
- `/permissions`、`/memory`、`/mcp`、`/hooks`で状態を確認する。
- デバッグログで読み込みエラーを確認する。

### CPU・メモリ使用量が多い

- 大きなタスクの区切りでClaude Codeを再起動する。
- `/context`でコンテキスト使用状況を確認する。
- `/compact`で会話を圧縮する。
- 別タスクは `/clear` または新しいセッションへ分ける。
- 大量のファイルやログを一度に読み込ませない。

出典: [インストールとログイン](https://code.claude.com/docs/ja/troubleshoot-install)、[設定のデバッグ](https://code.claude.com/docs/ja/debug-your-config)、[トラブルシューティング](https://code.claude.com/docs/ja/troubleshooting)

## 11. 参照した公式ドキュメント

- [概要](https://code.claude.com/docs/ja/overview)
- [クイックスタート](https://code.claude.com/docs/ja/quickstart)
- [高度なセットアップ](https://code.claude.com/docs/ja/setup)
- [CLIリファレンス](https://code.claude.com/docs/ja/cli-reference)
- [コマンド](https://code.claude.com/docs/ja/commands)
- [メモリ](https://code.claude.com/docs/ja/memory)
- [設定](https://code.claude.com/docs/ja/settings)
- [権限](https://code.claude.com/docs/ja/permissions)
- [Skills](https://code.claude.com/docs/ja/skills)
- [Hooks](https://code.claude.com/docs/ja/hooks)
- [MCP](https://code.claude.com/docs/ja/mcp)
- [設定のデバッグ](https://code.claude.com/docs/ja/debug-your-config)
- [インストールとログイン](https://code.claude.com/docs/ja/troubleshoot-install)
- [トラブルシューティング](https://code.claude.com/docs/ja/troubleshooting)

