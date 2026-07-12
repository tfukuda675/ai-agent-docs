# ドキュメントインデックス — opencode（Linux 向け）

opencode（オープンソースの AI コーディングエージェント）を **Linux で利用する際に必要な情報**を、公式ドキュメント（<https://opencode.ai/docs/ja/>）を基にトピック別に整理したもの。TUI での利用と CLI 自動化、設定・エージェント・権限・MCP を中心にカバーする。

| ファイル | 概要 | キーワードタグ |
| --- | --- | --- |
| [01-インストール.md](01-インストール.md) | インストール・起動・更新・アンインストール | `#install` `#curl` `#npm` `#brew` `#pacman` `#aur` `#upgrade` `#uninstall` `#clipboard` `#wayland` |
| [02-認証とプロバイダー.md](02-認証とプロバイダー.md) | API キー・プロバイダー・モデル選択 | `#auth` `#connect` `#provider` `#model` `#api-key` `#models-dev` `#small-model` `#variant` |
| [03-クイックスタート.md](03-クイックスタート.md) | 初回セッション・基本操作 | `#quickstart` `#init` `#agents-md` `#@mention` `#!bash` `#run` `#getting-started` |
| [04-CLIリファレンス.md](04-CLIリファレンス.md) | コマンド・フラグ・`opencode run` | `#cli` `#run` `#serve` `#attach` `#session` `#stats` `#export` `#import` `#env-vars` `#headless` |
| [05-TUIとキーバインド.md](05-TUIとキーバインド.md) | TUI 操作・リーダーキー・スラッシュコマンド | `#tui` `#leader-key` `#keybinds` `#slash-commands` `#tui-json` `#theme` `#editor` |
| [06-設定.md](06-設定.md) | opencode.json・優先順位・変数置換 | `#config` `#opencode-json` `#jsonc` `#precedence` `#env` `#file-substitution` `#schema` |
| [07-ルール-AGENTSmd.md](07-ルール-AGENTSmd.md) | AGENTS.md・instructions・CLAUDE.md 互換 | `#rules` `#agents-md` `#instructions` `#claude-md-fallback` `#init` `#global-rules` |
| [08-エージェント.md](08-エージェント.md) | Build/Plan・サブエージェント・カスタム | `#agents` `#build` `#plan` `#subagent` `#explore` `#scout` `#agent-create` `#frontmatter` |
| [09-コマンド.md](09-コマンド.md) | カスタムスラッシュコマンド | `#commands` `#template` `#arguments` `#shell-injection` `#file-reference` `#subtask` |
| [10-権限とツール.md](10-権限とツール.md) | permission 設定・ツール制御 | `#permissions` `#allow` `#ask` `#deny` `#pattern` `#wildcard` `#tools` `#agent-permission` |
| [11-MCP.md](11-MCP.md) | MCP サーバー（local/remote）・OAuth | `#mcp` `#local` `#remote` `#stdio` `#http` `#oauth` `#mcp-add` `#tools` |
| [12-トラブルシューティング.md](12-トラブルシューティング.md) | ログ・よくある問題・Linux 注意 | `#troubleshooting` `#logs` `#log-level` `#auth-error` `#model-not-found` `#cache` `#clipboard` `#wayland` |

## 読む順序の目安

1. [01-インストール.md](01-インストール.md) → [02-認証とプロバイダー.md](02-認証とプロバイダー.md) → [03-クイックスタート.md](03-クイックスタート.md)。
2. 日常操作は [04-CLIリファレンス.md](04-CLIリファレンス.md) / [05-TUIとキーバインド.md](05-TUIとキーバインド.md)。
3. カスタマイズは [06-設定.md](06-設定.md) / [07-ルール-AGENTSmd.md](07-ルール-AGENTSmd.md) / [08-エージェント.md](08-エージェント.md) / [09-コマンド.md](09-コマンド.md)。
4. 制御・連携は [10-権限とツール.md](10-権限とツール.md) / [11-MCP.md](11-MCP.md)。
5. 困ったら [12-トラブルシューティング.md](12-トラブルシューティング.md)。

## 出典・注意

- 参照元は opencode 公式日本語ドキュメント（<https://opencode.ai/docs/ja/>）。
- 記載の仕様・パス・バージョンは執筆時点（2026-07）のもの。最新は公式ドキュメントを参照すること。
- SDK・サーバー・プラグイン開発・Desktop/IDE/Web・GitHub/GitLab 連携などは対象外（必要に応じて別途）。
