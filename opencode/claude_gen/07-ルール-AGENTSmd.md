# 07. ルール（AGENTS.md）

> **概要**: プロジェクトの永続指示を与える `AGENTS.md` と、追加指示ファイルの取り込みをまとめる。
> **参照元**: <https://opencode.ai/docs/ja/rules>

opencode は **AGENTS.md** ファイルで LLM の挙動をプロジェクトに合わせて調整する（Cursor のルールや Claude Code の CLAUDE.md に相当）。

---

## 配置場所

| 種類 | 場所 | 適用範囲 |
| --- | --- | --- |
| プロジェクト | プロジェクトルートの `AGENTS.md` | そのディレクトリ・サブディレクトリでの作業時 |
| グローバル | `~/.config/opencode/AGENTS.md` | すべてのセッション（個人設定向け） |

### Claude Code 互換

`AGENTS.md` が無い場合、opencode は `CLAUDE.md`（プロジェクト）と `~/.claude/CLAUDE.md`（グローバル）にフォールバックする。Claude Code から移行する場合に便利。

### 読み込み順（各カテゴリで最初の一致が有効）

1. ローカルファイル（現在のディレクトリから上へ探索）
2. グローバル `~/.config/opencode/AGENTS.md`
3. Claude Code `~/.claude/CLAUDE.md`（無効化していない場合）

`AGENTS.md` は `CLAUDE.md` より優先される。

---

## 生成（`/init`）

TUI 内で `/init` を実行するとコードベースを分析して `AGENTS.md` を生成する。既存ファイルには追記を試みる。生成後は git にコミットしてチーム共有できる。

---

## 追加指示ファイルの取り込み（`instructions`）

`opencode.json` の `instructions` で、`AGENTS.md` に重複させずに追加のルールファイルを含められる:

```json
{
  "instructions": ["CONTRIBUTING.md", "docs/guidelines.md", "https://raw.github.com/.../rules.md"]
}
```

glob とリモート URL（5 秒タイムアウト）に対応。

---

## 外部ファイルの参照

`AGENTS.md` 内でシンボリック参照 `@docs/typescript-guidelines.md` を使うと、必要時にオンデマンドで外部ファイルを読み込ませられる。事前読み込みなしでモジュール化・再利用ができる。

---

## 関連ドキュメント

- [03-クイックスタート.md](03-クイックスタート.md) — `/init`
- [06-設定.md](06-設定.md) — `instructions`
- [08-エージェント.md](08-エージェント.md) — エージェント固有のプロンプト
- [09-コマンド.md](09-コマンド.md) — 定型ワークフロー
