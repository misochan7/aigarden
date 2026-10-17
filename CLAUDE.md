# AIGARDEN Crypto Tools — 作業ルール

このリポジトリは個人サイト `https://ai-garden-lab.com/` の静的HTMLです。
ビルドツールは使っていません。HTMLを直接書いて、そのまま公開されます。

---

## ディレクトリ構成

```
/
├── CLAUDE.md                      このファイル
├── .github/
│   ├── workflows/research.yml     Issueから記事PRを自動作成するワークフロー
│   └── ISSUE_TEMPLATE/research.yml 記事依頼用のIssueフォーム
├── index.html                     AIGARDEN トップ
├── images/
│   └── pfp.png ほか               プロフィール画像・ロゴ等
└── crypto/
    ├── index.html                 Crypto Tools トップ（ツール一覧）
    ├── fdv/                       FDV計算ツール
    ├── perptool/                  Perpツール
    ├── prompt/                    Crypto AIプロンプト集
    ├── prediction/                Prediction ダッシュボード
    └── research/
        ├── index.html             リサーチ記事の一覧ページ（時系列フィード）
        ├── _template.html         記事テンプレート（絶対に編集しない）
        └── {slug}.html            個別のリサーチ記事
```

※ 実際の構成と違う場合はこのセクションを直してから作業すること。

---

## 一番よくやる作業：リサーチ記事の作成

**トリガー**：Xのポスト本文とURLを渡されて「記事にして」と言われたとき。
GitHub Actions から起動された場合は、Issue本文（フォーム）に同じ情報が入っている。

### 手順

1. `crypto/research/_template.html` をコピーして `crypto/research/{slug}.html` を作る
2. テンプレ内の `【 】` で囲まれたプレースホルダをすべて埋める（下の対応表）
3. テンプレ内の説明用コメントは削除する：
   - ファイル冒頭の `<!-- ===== リサーチ記事テンプレート ... ===== -->` ブロック
   - `<!-- 【1】... -->` 〜 `<!-- 【12】... -->` の番号付きコメント
   - 「セクションの構成は自由…」のブロックコメント、`<!-- 外部リンクボタン（…） -->`、`<!-- 招待コードなどあれば: … -->`
   - `<!-- ARTICLE HEADER -->` `<!-- 前後ナビ -->` のような構造コメントは残してよい
4. `crypto/research/index.html` のフィード（`<ul class="feed-list" id="feedList">` の直後）の**先頭**に新しい `<li>` を追加する（下の「一覧カード」参照）
5. 差分を要約して報告する
6. **確認を取ってから** commit / push する（GitHub Actions では、PRを作ることが確認の代わり。main へは直接pushしない）

### slug（ファイル名）の決め方

- プロジェクト名を小文字英数字とハイフンだけにする
- 例：`Monad` → `monad.html` / `Hyperliquid` → `hyperliquid.html`
- 同名ファイルが既にある場合は上書きせず、既存記事の更新なのか新規なのかを聞く
  - GitHub Actions では聞けないので、`{slug}2.html` のように末尾に番号を付けて新規作成し、PR本文で「同名の既存記事あり」と明記する（既存の `xstocks2.html` と同じ流儀）

### プレースホルダ対応表

| 箇所 | 埋める内容 |
|---|---|
| `<title>` | `{プロジェクト名}リサーチ — {一言説明} \| AIGARDEN` |
| `<meta description>` | 120〜160文字。プロジェクト名・チェーン名・カテゴリを自然に含める |
| `og:title` | `{プロジェクト名}リサーチ — {一言説明}`（`\| AIGARDEN` は付けない） |
| `og:description` | SNS向けに1〜2文 |
| `og:url` | `https://ai-garden-lab.com/crypto/research/{slug}.html` |
| パンくず | プロジェクト名のみ |
| `.project-tag` | `{チェーン} / {カテゴリ}` 例：`Solana / DeFi`、`Ethereum / L2`、`Bitcoin / L1` |
| `<h1>` | `{プロジェクト名} — {一言説明}` |
| `.lead` | 2〜3行。読んだ人が「何のプロジェクトか」すぐ分かる文 |
| `.notice-date` | `調査時点：YYYY年MM月DD日 午前/午後HH:MM`（**元Xポストの投稿日時**。今日の日付ではない） |
| 注意文中の「元のポスト」リンク | 参照した元ポストのURL |
| 下部 `.ext-link` | 公式サイト or Waitlist（または招待リンク）のURLとボタンテキスト。無ければ**`<a>`ごと削除**。招待コードがあるときだけ、ボタンテキストの後に `<span class="code-note">コード：XXXXXX</span>` を足す |
| 下部 `.x-btn` | 参照したXポストのURL |

### 一覧カード（`research/index.html`）

既存の `<li>` をそのままコピーして中身だけ差し替える：

```html
          <li class="feed-item cat-research" data-cat="research">
            <a href="{slug}.html" class="feed-link">
              <span class="feed-date">YYYY/M/D</span>
              <span class="feed-main">
                <span class="feed-title">{h1と同じ文言}</span>
              </span>
              <span class="feed-arrow">→</span>
            </a>
          </li>
```

- カテゴリは `research` / `security` / `tips` の3種類。`class="feed-item cat-xxx"` と `data-cat="xxx"` の両方を揃える
- `feed-date` は元ポストの投稿日（`2026/7/25` のように月日はゼロ埋めしない）

### セクション構成

テンプレの4セクション（どんなサービスか／詳細／懸念点／現時点の判断）は目安。
内容に合わせて増減・見出し変更してよい。ただし：

- **「現時点の判断」（`.verdict-box`）は必ず残す**
- 懸念が本当に無いときだけ「懸念点」セクションを削除する。無理に埋めない
- 懸念点は `concern-box` の中の `<ul>` に書く
- 詳細セクションは必要なら複数作ってよい
- 段落は `<p>`、箇条書きは `<ul class="point-list">` を使う

---

## 文章のルール

- **元ポストに書いてある事実だけを書く。** 推測で補完しない
- 元ポストの情報が足りない部分は、埋めずに「元ポストでは触れられていない」と報告する（Actions ではPR本文に書く）。勝手にWeb検索して混ぜない（検索してほしいときは明示的に指示する）
- 断定を避ける。「〜と見られる」「〜とされている」
- 価格予想・利回りの約束・「買い」「売り」などの投資助言は書かない
- トーンは冷静で淡々と。煽らない。絵文字は使わない（テンプレ既存の「⚠︎」は残す）
- 一人称は使わず、「現時点の判断」セクションだけ主観でよい（「触ってみる」「様子見」「見送り」など）

---

## 触ってはいけないもの

- `_template.html` — 記事を作るときにコピー元として読むだけ。編集しない
- 各HTMLの `<style>` ブロック内のCSS — デザイン変更を明示的に頼まれない限り触らない
- CSS変数（`--accent: #0d5f66` など）の値
- ヘッダー / フッター / プロフィールセクションの構造
- `fdv/` `perptool/` `prompt/` `prediction/` 配下 — リサーチ記事の作業では一切触らない
- `.github/` 配下 — 記事作成の作業では触らない

---

## Git の扱い

- 手元で作業するときは `main` に直接コミットしてよい
- GitHub Actions では `research/{slug}` ブランチを切ってPRを作る
- **push は毎回、実行前に確認を取る**（Actions ではPRのMergeが確認）
- コミットメッセージは日本語1行。プレフィックスを付ける：
  - `research: {プロジェクト名}の記事を追加`
  - `research: {プロジェクト名}の記事を更新`
  - `index: ツール一覧を更新`
  - `fix: {内容}`
- 1記事＝1コミット。一覧ページの更新も同じコミットに含める
- コミットメッセージに Claude の署名や Co-Authored-By は入れない

---

## 記事を書き終えたら確認すること

- [ ] `【` `】` が1つも残っていない（冒頭の説明コメントにも含まれているので、消し忘れるとここで引っかかる）
- [ ] テンプレの説明用HTMLコメント（冒頭ブロック、`<!-- 【n】... -->` など）を消した
- [ ] `og:url` のファイル名が実際のファイル名と一致している
- [ ] 「調査時点」が元ポストの投稿日時になっている（今日の日付になっていないか）
- [ ] 外部リンクすべてに `target="_blank" rel="noopener noreferrer"` が付いている
- [ ] 使わなかったセクション／ボタンのブロックを削除した
- [ ] `research/index.html` のフィード先頭に新記事の `<li>` を追加した（カテゴリのclassとdata-catが一致）
- [ ] 相対パス（`../../images/pfp.png` など）が階層と合っている
