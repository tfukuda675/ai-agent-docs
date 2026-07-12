# 15. TUI（ターミナル UI）

> **概要**: Claude Code のターミナル UI（TUI）まわり — フルスクリーンレンダリング（`/tui`）、ターミナル設定、テーマ、ステータスライン — を Linux 視点でまとめる。
> **参照元**: <https://code.claude.com/docs/ja/fullscreen> / <https://code.claude.com/docs/ja/terminal-config> / <https://code.claude.com/docs/ja/statusline>

Claude Code の CLI は対話時にターミナル上で動く TUI（テキストユーザーインターフェース）を提供する。標準の「クラシックレンダラー」に加え、`vim` や `htop` のように代替スクリーンに描画する**フルスクリーンレンダリング**を選べる。このファイルは表示・スクロール・テーマ・ステータスラインなど TUI 固有の設定を扱う（キー操作の一覧は [05-インタラクティブモード.md](05-インタラクティブモード.md)）。

---

## フルスクリーンレンダリング（`/tui`）

`vim` のように代替スクリーンバッファに描画し、**ちらつきを排除**、長い会話でもメモリを一定に保ち、マウスサポートを追加するモード（リサーチプレビュー、v2.1.89+）。VS Code 統合ターミナル・tmux・iTerm2 などで効果が大きい。

> 「フルスクリーン」は描画方式の名称で、ウィンドウ最大化とは無関係（任意のサイズで動く）。

### 有効化・切替

```text
/tui fullscreen    # 現在の会話を保ったままフルスクリーンで再起動
/tui default       # クラシックレンダラーに戻す
/tui               # 現在のレンダラーを確認
```

環境変数・設定でも指定可:

```bash
CLAUDE_CODE_NO_FLICKER=1 claude
```
```json
{ "env": { "CLAUDE_CODE_NO_FLICKER": "1" } }
```

クラシックを強制: `CLAUDE_CODE_DISABLE_ALTERNATE_SCREEN=1`。（バックグラウンドセッションの `claude attach` は常にフルスクリーン。）

### 変わる挙動

| クラシック | フルスクリーン |
| --- | --- |
| 入力ボックスが出力に押されて移動 | 入力ボックスが画面下部に固定 |
| ターミナルの `Cmd+f`/tmux 検索 | `Ctrl+O` でトランスクリプトモード → `/` 検索、または `[` でスクロールバックへ書き出し |
| ネイティブのクリック&ドラッグ選択 | アプリ内選択（リリースで自動コピー） |
| `Cmd` クリックで URL | macOS は `Cmd`、**Linux/Windows は `Ctrl`** クリック |

### スクロール

| ショートカット | アクション |
| --- | --- |
| `PgUp` / `PgDn` | 半画面スクロール |
| `Ctrl+Home` / `Ctrl+End` | 会話の先頭 / 最新へ（自動フォロー再開） |
| マウスホイール | 数行スクロール |

- 上にスクロールすると自動フォローが一時停止。`Ctrl+End` か最下部で再開。完全オフは `/config` の Auto-scroll。
- ホイールが速すぎ/遅すぎる: `export CLAUDE_CODE_SCROLL_SPEED=3`（1〜20、`vim` 相当は 3）、または `/scroll-speed` で対話調整。加速オフは `settings.json` の `wheelScrollAccelerationEnabled: false`。

### 会話の検索・レビュー（トランスクリプトモード）

`Ctrl+O` で切替。`less` 風ナビゲーション:

| キー | アクション |
| --- | --- |
| `/` | 検索（`Enter` 確定 / `Esc` 取消） |
| `n` / `N` | 次 / 前の一致 |
| `j`/`k`, `g`/`G`, `Ctrl+u`/`Ctrl+d` | 行/先頭末尾/半ページ |
| `[` | 全会話をターミナルのネイティブスクロールバックへ書き出し（`Cmd+f`・tmux で検索可能に） |
| `v` | `$VISUAL`/`$EDITOR` で開く |
| `Ctrl+O` / `Esc` / `q` | トランスクリプトモード終了 |

`/focus` でツール呼び出しを1行要約する静かなビューに切替（セッション間で保持）。

### マウス選択とクリップボード（Linux）

- 選択はリリース時に自動コピー。オフは `/config` の Copy on select。手動コピーは `Ctrl+Shift+c`。
- **Linux のクリップボード書き込み**: Wayland は `wl-copy`、X11 は `xclip` または `xsel`（どちらかをインストール）。PRIMARY 選択にも書くので中クリックペースト可。SSH 経由は OSC 52 にフォールバック。
- マウスキャプチャがネイティブ選択の邪魔になる場合:
  - `CLAUDE_CODE_DISABLE_MOUSE=1` — マウスキャプチャをオプトアウト（ちらつきなし描画は維持）。
  - `CLAUDE_CODE_DISABLE_MOUSE_CLICKS=1` — ホイールは残しクリック/ドラッグのみ無効。
  - ネイティブ選択の一時利用は、ほとんどのターミナルで `Shift`+ドラッグ。

### tmux での注意

- マウスホイールには tmux のマウスモードが必要。`~/.tmux.conf` に `set -g mouse on`。
- iTerm2 の tmux 統合モード（`tmux -CC`）とは非互換。通常の tmux は問題なし。
- tmux 3.4+ を推奨（同期出力でちらつき低減）。`tmux -V` で確認。

---

## ターミナル設定（`/terminal-setup`）

Claude Code はどのターミナルでも設定なしで動くが、以下は必要に応じて。

### 複数行入力

- `Ctrl+J`、または `\` の後 `Enter` は全ターミナルで機能。
- `Shift+Enter`: Ghostty/Kitty/iTerm2/WezTerm/Warp/Apple Terminal/Windows Terminal はそのまま。VS Code/Cursor/Alacritty/Zed は `/terminal-setup` を1回実行。gnome-terminal や JetBrains IDE 端末は不可（`Ctrl+J` を使う）。
- tmux 内では別途 tmux 設定が必要（下記）。

### 通知（ターミナルベル / デスクトップ通知）

- 既定でデスクトップ通知を送るのは Ghostty/Kitty/iTerm2 のみ。他は `settings.json` の `preferredNotifChannel: "terminal_bell"`、または通知フックを使う。

```json
{
  "hooks": {
    "Notification": [
      { "hooks": [{ "type": "command", "command": "notify-send 'Claude Code' 'Claude Code needs your attention'" }] }
    ]
  }
}
```

（フックの詳細は [10-フック.md](10-フック.md)）

### tmux の設定

`~/.tmux.conf` に追加して `tmux source-file ~/.tmux.conf`:

```bash
set -g allow-passthrough on
set -s extended-keys on
set -as terminal-features 'xterm*:extkeys'
```

`allow-passthrough` で通知・プログレスバーが外側ターミナルに届き、`extended-keys` で `Shift+Enter` が使える。

### 表示のちらつき

ちらつく/スクロール位置が飛ぶ場合はフルスクリーンレンダリング（上記 `/tui fullscreen`）へ。

---

## テーマ（`/theme`）

`/theme` または `/config` のテーマピッカーで選択。`auto` はターミナルの明暗を検出して OS の外観に追従する（ターミナル自体の配色は制御しない）。

### カスタムテーマ（v2.1.118+）

各テーマは `~/.claude/themes/<slug>.json`。ファイル名がスラッグで、選択すると `custom:<slug>` が保存される。フィールドは `name` / `base`（`dark`, `light`, `dark-daltonized`, `light-daltonized`, `dark-ansi`, `light-ansi`）/ `overrides`。

```json
{
  "name": "Dracula",
  "base": "dark",
  "overrides": {
    "claude": "#bd93f9",
    "error": "#ff5555",
    "success": "#50fa7b"
  }
}
```

- カラー値: `#rrggbb` / `#rgb` / `rgb(r,g,b)` / `ansi256(n)` / `ansi:<name>`。不明トークンや無効値は無視される。
- `~/.claude/themes/` は監視され、編集は再起動なしで反映。
- 主なトークン: `claude`（ブランドアクセント）, `text`, `error`/`success`/`warning`, `promptBorder`/`planMode`/`autoAccept`/`bashBorder`, `diffAdded`/`diffRemoved`, フルスクリーン用 `userMessageBackground` など。サブエージェント色は `<color>_FOR_SUBAGENTS_ONLY`。

---

## ステータスライン（`/statusline`）

画面下部に、設定したシェルスクリプトの出力を表示する行。stdin で JSON セッションデータを受け取り、モデル・コンテキスト使用率・コスト・git ブランチなどを一目で見られる。**ローカル実行で API トークンを消費しない**。

### 設定

```text
/statusline show model name and context percentage with a progress bar
```
または `settings.json` を手動編集:

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 2
  }
}
```

- `refreshInterval`（秒）でイベント駆動更新に加えて定期再実行。`hideVimModeIndicator` で組み込みの `-- INSERT --` を隠す。
- 無効化: `settings.json` から `statusLine` を削除、または `/statusline delete`。

### スクリプト例（Bash）

```bash
#!/bin/bash
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name')
DIR=$(echo "$input" | jq -r '.workspace.current_dir')
PCT=$(echo "$input" | jq -r '.context_window.used_percentage // 0' | cut -d. -f1)
echo "[$MODEL] 📁 ${DIR##*/} | ${PCT}% context"
```
```bash
chmod +x ~/.claude/statusline.sh
```

インラインコマンドも可:

```json
{ "statusLine": { "type": "command", "command": "jq -r '\"[\\(.model.display_name)] \\(.context_window.used_percentage // 0)% context\"'" } }
```

### 仕組みと入力データ

- 新しいアシスタントメッセージ後・`/compact` 完了後・権限モード変更・vim モード切替で実行（300ms デバウンス）。
- stdout に出力したテキストを表示。複数 `echo` で複数行、ANSI エスケープで色、OSC 8 でクリック可能リンク。
- ターミナル幅は `COLUMNS` / `LINES` 環境変数で取得（`tput cols` は不可）。
- 主な JSON フィールド: `model.display_name`, `workspace.current_dir`/`project_dir`, `cost.total_cost_usd`/`total_duration_ms`, `context_window.used_percentage`/`context_window_size`, `effort.level`, `vim.mode`, `pr.number`/`pr.review_state`, `session_id`, `version` など。null になり得るフィールドは `// 0` 等でフォールバックする。

### テスト・注意

- モック入力でテスト:
  ```bash
  echo '{"model":{"display_name":"Opus"},"workspace":{"current_dir":"/home/user/project"},"context_window":{"used_percentage":25},"session_id":"test"}' | ./statusline.sh
  ```
- 出力は短く保つ。`git status` など遅い操作はキャッシュ（`session_id` をキャッシュキーに）。
- **ワークスペーストラスト**: `statusLine` はシェルを実行するため、フックと同様に信頼ダイアログ受諾後に有効（未受諾だと `statusline skipped · restart to fix`）。`disableAllHooks: true` だと無効。
- サブエージェント用の行は `subagentStatusLine` 設定でカスタマイズ可能。

---

## 関連ドキュメント

- [05-インタラクティブモード.md](05-インタラクティブモード.md) — キーボードショートカット・Vim
- [06-設定.md](06-設定.md) — `settings.json`・`statusLine`・`env`
- [10-フック.md](10-フック.md) — 通知フック
- [14-トラブルシューティング.md](14-トラブルシューティング.md) — 表示の文字化け・ちらつき
