# 05. TUI とキーバインド

> **概要**: opencode の TUI（ターミナル UI）の使い方、リーダーキー、スラッシュコマンド、キーバインドをまとめる。
> **参照元**: <https://opencode.ai/docs/ja/tui> / <https://opencode.ai/docs/ja/keybinds>

`opencode` または `opencode /path/to/project` で TUI を起動する。opencode は TUI を中心とした対話型ツール。

---

## 基本操作

- **ファイル参照 `@`**: メッセージ内で `@` を使うと作業ディレクトリ内をファジー検索してファイルを参照。
- **シェルコマンド `!`**: メッセージを `!` で始めるとシェルコマンドを実行し、出力を会話に取り込む。
- **外部エディタ**: `EDITOR` 環境変数を設定しておくと `/editor` で外部エディタを使える（例: `export EDITOR="code --wait"`）。

---

## リーダーキー

ターミナルとの衝突を避けるため、多くのコマンドは **リーダーキー**（既定 `ctrl+x`）を先に押してからショートカットを押す（例: `ctrl+x` → `n` で新規セッション）。リーダーキーのタイムアウト（既定 2000ms）は `tui.json` で設定する。

---

## 主なスラッシュコマンド

| コマンド | 機能 | キーバインド |
| --- | --- | --- |
| `/connect` | プロバイダー・API キーの追加 | — |
| `/models` | モデル一覧 | `ctrl+x m` |
| `/init` | AGENTS.md の作成/更新 | `ctrl+x i` |
| `/sessions` | セッション一覧・切替 | `ctrl+x l` |
| `/new` | 新規セッション | `ctrl+x n` |
| `/compact` | 現在のセッションを圧縮 | `ctrl+x c` |
| `/undo` | 直前のメッセージを取り消し | `ctrl+x u` |
| `/redo` | 取り消しをやり直し | `ctrl+x r` |
| `/editor` | 外部エディタを開く | `ctrl+x e` |
| `/export` | 会話を Markdown でエクスポート | `ctrl+x x` |
| `/share` | セッションを共有 | `ctrl+x s` |
| `/help` | ヘルプ | `ctrl+x h` |
| `/exit` | 終了 | `ctrl+x q` |

---

## 主なキーバインド（既定）

| アクション | 既定バインド |
| --- | --- |
| 新規セッション | `<leader>n` |
| セッション一覧 | `<leader>l` |
| モデル一覧 | `<leader>m` |
| 直近モデルへ循環 | `f2` |
| エージェント循環 | `tab` |
| エディタを開く | `<leader>e` |
| メッセージをコピー | `<leader>y` |
| 元に戻す / やり直す | `<leader>u` / `<leader>r` |
| 入力クリア | `ctrl+c` |
| 貼り付け | `ctrl+v` |
| 改行 | `shift+return`, `ctrl+return` |
| ページ上 / 下 | `pageup` / `pagedown`（`ctrl+alt+b` / `ctrl+alt+f`） |
| 終了 | `ctrl+c`, `ctrl+d`, `<leader>q` |

（`<leader>` は既定 `ctrl+x`）

---

## TUI の設定（`tui.json`）

TUI 固有の設定は `tui.json` / `tui.jsonc`（スキーマ `https://opencode.ai/tui.json`）で行う。`OPENCODE_TUI_CONFIG` でパスを指定できる。設定できる主な項目:

- テーマ選択（[06-設定.md](06-設定.md) 参照）
- キーバインド（既定にマージされる。無効化は値を `"none"`）
- スクロール速度・加速
- マウスキャプチャの切替
- デスクトップ通知・サウンド（Attention 機能）
- リーダーキーのタイムアウト

---

## 関連ドキュメント

- [03-クイックスタート.md](03-クイックスタート.md) — 初回操作
- [06-設定.md](06-設定.md) — `tui.json`・テーマ
- [08-エージェント.md](08-エージェント.md) — `Tab` でのエージェント切替
- [09-コマンド.md](09-コマンド.md) — カスタムスラッシュコマンド
