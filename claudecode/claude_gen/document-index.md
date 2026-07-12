# ドキュメントインデックス — Claude Code CLI（Linux 向け）

Claude Code CLI を **Linux で利用する際に必要な情報**を、公式ドキュメント（<https://code.claude.com/docs/ja/>）を基にトピック別に整理したもの。カバー範囲は **コア＋拡張機能＋TUI**（MCP / フック / スキル / サブエージェント / 権限・サンドボックス、および TUI＝ターミナル UI＝フルスクリーン・テーマ・ステータスラインを含む）。SDK・クラウドプロバイダ・CI/CD・Desktop/Web/IDE 連携は対象外。

各ファイルはキーワードタグ付きで下に索引化している。

| ファイル | 概要 | キーワードタグ |
| --- | --- | --- |
| [01-インストール.md](01-インストール.md) | インストール・確認・更新・アンインストール | `#install` `#setup` `#curl` `#apt` `#dnf` `#apk` `#npm` `#nodejs` `#alpine` `#musl` `#update` `#channel` `#uninstall` `#doctor` `#requirements` |
| [02-認証.md](02-認証.md) | アカウント種別・ログイン・認証情報・CI トークン | `#auth` `#login` `#logout` `#credentials` `#oauth` `#setup-token` `#api-key` `#apiKeyHelper` `#ssh` `#precedence` |
| [03-クイックスタート.md](03-クイックスタート.md) | 初回セッションと基本操作 | `#quickstart` `#getting-started` `#first-session` `#basic-usage` `#essential-commands` `#git` |
| [04-CLIリファレンス.md](04-CLIリファレンス.md) | サブコマンド・フラグ・ヘッドレス実行 | `#cli` `#commands` `#flags` `#headless` `#print` `#-p` `#pipe` `#resume` `#continue` `#model` `#output-format` |
| [05-インタラクティブモード.md](05-インタラクティブモード.md) | ショートカット・Bashモード・Vim・履歴 | `#interactive` `#keyboard-shortcuts` `#bash-mode` `#!` `#@mention` `#vim` `#history` `#background` `#btw` `#recap` |
| [06-設定.md](06-設定.md) | settings.json・環境変数・優先順位 | `#settings` `#settings-json` `#env-vars` `#environment` `#precedence` `#model-config` `#statusline` `#telemetry` `#proxy` |
| [07-メモリ-CLAUDEmd.md](07-メモリ-CLAUDEmd.md) | CLAUDE.md・自動メモリ・rules | `#memory` `#claude-md` `#auto-memory` `#rules` `#import` `#init` `#agents-md` `#context` |
| [08-よく使うワークフロー.md](08-よく使うワークフロー.md) | プロンプトレシピ・ベストプラクティス | `#workflows` `#best-practices` `#prompting` `#plan-mode` `#verify` `#context-management` `#compact` `#clear` `#parallel` `#fan-out` |
| [09-MCP.md](09-MCP.md) | MCP サーバー接続・認証・リソース | `#mcp` `#mcp-add` `#stdio` `#http` `#sse` `#mcp-json` `#scope` `#oauth` `#tool-search` `#resources` |
| [10-フック.md](10-フック.md) | フックの仕組み・イベント・設定 | `#hooks` `#pretooluse` `#posttooluse` `#sessionstart` `#matcher` `#exit-code` `#auto-format` `#block` `#automation` |
| [11-スキルとスラッシュコマンド.md](11-スキルとスラッシュコマンド.md) | SKILL.md・スラッシュコマンド一覧 | `#skills` `#skill-md` `#slash-commands` `#frontmatter` `#arguments` `#bundled-skills` `#code-review` `#doctor` |
| [12-サブエージェント.md](12-サブエージェント.md) | サブエージェントの作成・実行 | `#subagents` `#agents` `#explore` `#plan` `#general-purpose` `#delegation` `#isolated-context` `#background` |
| [13-権限とセキュリティ.md](13-権限とセキュリティ.md) | 権限ルール・モード・サンドボックス | `#permissions` `#permission-modes` `#allow` `#deny` `#ask` `#acceptEdits` `#plan` `#auto` `#bypassPermissions` `#sandbox` `#protected-paths` `#security` |
| [14-トラブルシューティング.md](14-トラブルシューティング.md) | インストール/ログイン/実行時の問題 | `#troubleshooting` `#path` `#command-not-found` `#ripgrep` `#login-error` `#403` `#memory` `#doctor` `#debug` `#tls` `#proxy` |
| [15-TUI-ターミナルUI.md](15-TUI-ターミナルUI.md) | フルスクリーン・ターミナル設定・テーマ・ステータスライン | `#tui` `#fullscreen` `#tui-command` `#no-flicker` `#transcript` `#scroll` `#mouse` `#terminal-setup` `#shift-enter` `#tmux` `#theme` `#statusline` `#notification` |

## 読む順序の目安

1. まず [01-インストール.md](01-インストール.md) → [02-認証.md](02-認証.md) → [03-クイックスタート.md](03-クイックスタート.md)。
2. 日常操作は [04-CLIリファレンス.md](04-CLIリファレンス.md) / [05-インタラクティブモード.md](05-インタラクティブモード.md) / [08-よく使うワークフロー.md](08-よく使うワークフロー.md)。
3. カスタマイズは [06-設定.md](06-設定.md) / [07-メモリ-CLAUDEmd.md](07-メモリ-CLAUDEmd.md) / [10-フック.md](10-フック.md) / [11-スキルとスラッシュコマンド.md](11-スキルとスラッシュコマンド.md) / [12-サブエージェント.md](12-サブエージェント.md)。
4. 外部連携・安全性は [09-MCP.md](09-MCP.md) / [13-権限とセキュリティ.md](13-権限とセキュリティ.md)。
5. ターミナル UI（表示・テーマ・ステータスライン）は [15-TUI-ターミナルUI.md](15-TUI-ターミナルUI.md)。
6. 困ったら [14-トラブルシューティング.md](14-トラブルシューティング.md)。

## 出典・注意

- 参照元は Claude Code 公式日本語ドキュメント（<https://code.claude.com/docs/ja/>）。ドキュメント一覧は <https://code.claude.com/docs/llms.txt>。
- 記載のバージョン番号（例 v2.1.x）や仕様は執筆時点（2026-07）のもの。最新は公式ドキュメントを参照すること。
- Windows/macOS 固有、Desktop/Web/IDE、SDK、クラウドプロバイダの記述は原則省略している。
