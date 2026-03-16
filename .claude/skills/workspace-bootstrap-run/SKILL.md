---
name: workspace-bootstrap-run
description: >
  セットアップ済みのワークスペースを起動する汎用エンジン。
  ~/.claude/workspace-bootstrap/sites.yaml を読み込み、
  Claude in Chrome MCP で全サイトをカテゴリ順に起動する。

  以下のケースでこのスキルを使うこと：
  - ユーザーが「ワークスペース起動」「ブラウザ開いて」「bootstrap」に言及したとき
  - セットアップ済みのユーザーが作業環境を復元したいとき
  - Claude in Chrome での作業開始前に環境構築が必要なとき
  - ユーザーが自動生成されたパーソナルコマンドを実行したとき
---

# Workspace Bootstrap — Run Engine

**セットアップ済みAIワークスペースを一発起動する実行エンジン**

---

## 前提

このスキルは `/workspace-bootstrap-setup` でセットアップが完了していることを前提とする。
設定ファイルが存在しない場合は、セットアップウィザードの実行を案内する。

---

## 実行フロー

### Phase 1: 設定ファイル読み込み

```
1. ~/.claude/workspace-bootstrap/sites.yaml を読み込む
2. ファイルが存在しない場合:
   → 「設定が見つかりません。/workspace-bootstrap-setup を実行してください」と表示
   → スキルを終了
3. profile.name を取得（Chrome プロファイル名）
4. categories を取得（サイト一覧）
5. 全サイト数・カテゴリ数をカウント
```

### Phase 2: MCP 接続確認

```
1. tabs_context_mcp を実行して Claude in Chrome MCP の接続を確認
2. 接続できない場合:
   → 「Claude in Chrome が接続されていません」と表示
   → 「Chrome プロファイル "{profile_name}" で Claude in Chrome を起動してください」
   → スキルを終了
```

### Phase 3: サイト起動

カテゴリ順にサイトを起動する。

```
各カテゴリについて：
  1. カテゴリヘッダーを表示:
     「📂 Opening {category_label}...」

  2. カテゴリ内の各サイトについて：
     a. tabs_create_mcp で新規タブを作成
     b. navigate で URL に遷移
     c. タブIDとサービス名を記録
     d. 「  ✓ {service_name}」と表示

  3. カテゴリ完了メッセージ:
     「  ✅ {category_label}: {count} sites opened」
```

### Phase 4: 状態確認

全サイト起動後、各タブの状態を簡易チェックする：

```
各タブについて：
  1. 起動後 1-2 秒待機（ページロード待ち）
  2. ページタイトルを取得（可能な範囲で）
  3. 以下を判定：
     - タイトル取得可 → ✅ opened
     - リダイレクトされた → ⚠️ redirected（URL 表示）
     - ブロックされた → ❌ blocked（例: Stripe の安全制限）
     - タイムアウト → ⚠️ timeout
```

### Phase 5: 完了レポート

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✅ Workspace Bootstrap Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Profile: {profile_name}
  Sites opened: {success_count}/{total_count}

  Category       | Sites                          | Status
  ──────────────|───────────────────────────────|──────
  AI             | ChatGPT, Claude                | ✅
  Creation       | Midjourney, Canva              | ✅
  Development    | GitHub, Vercel                 | ✅
  Communication  | X, Instagram                   | ⚠️ 1 issue

  ⚠️ Issues:
  - Stripe: Blocked by safety restriction (open manually)

  💡 Tips:
  - First time? Log in to each site with the account shown in config
  - Cookies will remember your login for next time
  - Re-run /workspace-bootstrap-setup to add more services

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Account Strategy（なぜ Google 同期しないのか）

```
❌ よくある間違い:
   Chrome を Google アカウントに同期
   → すべてのサイトが同じ Google アカウントになる
   → サイトごとの最適アカウントが活かされない
   → 有料プランの機能を使い損ねている

✅ このスキルの解決策:
   Google 同期しない専用プロファイルを作る
   → サイトごとに最適アカウントでログイン
   → Cookie でログイン状態を保持
   → 起動コマンド一発で復元
```

---

## 安全制限への対応

一部のサイトは Claude in Chrome の安全制限でアクセスできない：

| サイト | 制限 | 対応 |
|--------|------|------|
| Stripe Dashboard | 金融サイトブロック | レポートに表示し手動で案内 |
| 銀行系サイト | 金融サイトブロック | レポートに表示し手動で案内 |

ブロックされたサイトはスキップし、完了レポートに「手動で開いてください」と表示する。
エラーで停止しないこと。

---

## MCP 依存

以下のツールが利用可能であること：

| ツール | 用途 |
|--------|------|
| `tabs_create_mcp` | 新規タブ作成 |
| `tabs_context_mcp` | 現在のタブ状態取得 |
| `navigate` | URL 遷移 |
| `read_page` | ページ要素確認 |
| `get_page_text` | ページテキスト取得 |

---

## 制約事項

- パスワード入力は一切行わない
- ログイン操作は行わない（Cookie による復元のみ）
- Chrome プロファイルの自動作成はしない
- ブロックされたサイトはスキップする（停止しない）
- Google 同期が有効なプロファイルでの使用は推奨しない
