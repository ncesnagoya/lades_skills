# skill の一覧と使い方

アーキテクチャ仕様書の作成の全体フロー（全体ループ方式）を進めるための skill である。
全体フローの文書の 6章のモジュール（工程・判定・人間チェック）ごとに 1 つずつ、計 12 個ある。

- **正は全体フローの文書である。** skill は、文書の各モジュールを手順とツールの呼び出しにしたもの。
  skill と文書が食い違う場合は文書に従い、食い違いを `problems/` に記録する（使う側では skill を直さない）。
- skill は、その場面になると説明文（SKILL.md の冒頭の `description`）から呼ばれる。人間が `/<skill 名>` で呼んでもよい。
- ツールは `work/tools/` にある（skill の手順から呼ぶ）。
- 置き場: 原本は段階フォルダ直下の `work/skills/`、パターンフォルダでは `.claude/skills/` に写して使う。
  skill を作った経緯と改定の記録は、原本の側の `work/skills/作成の記録.md` にある（パターンフォルダへは写さない）。

## 全体フローと skill

```text
 ┌─────────────── 第N回（1〜5 をループ）───────────────┐
 │ m1-spec-analysis → m2-function-list → m3-means-design │
 │   → j-missing-info ─(未解決あり)→ m4-qa ─┐            │
 │                   └(なし)──────────────→ m5-means-list │
 │   → e-loop-exit                                       │
 └──────┬────────────────────────────────────────────────┘
        │ 終了しない: 止まる → h1-internal-review（H1 社内レビュー）
        │                      → h2-requester-answer（H2 要求者の回答）→ 第N+1回へ
        │ 終了する
        ▼
   m6-means-selection → m7-arch-spec → 止まる → h3-spec-review（H3 仕様書のレビュー）
```

| skill | 全体フローの箇所 | 使う場面 | 主な出力 | 終えた後 |
|---|---|---|---|---|
| [m1-spec-analysis](m1-spec-analysis/SKILL.md) | 工程 1 仕様設計書の解析 | 第N回の 1〜5 を始めるとき | `work/functions/analysis/解析結果.md` | m2 へ |
| [m2-function-list](m2-function-list/SKILL.md) | 工程 2 機能リスト化 | 解析結果から機能（`FN-`）と全体条件（`GC-`）を作るとき | `work/functions/items/機能リスト.md` | m3 へ |
| [m3-means-design](m3-means-design/SKILL.md) | 工程 3 機能の実現手段の考案 | 共通論点・共通方式・機能ごとの手段案（`MN-`）を考えるとき | `work/means/実現手段案.md`、`work/means/レビュー/` | j へ |
| [j-missing-info](j-missing-info/SKILL.md) | 判定 J 不足情報の判定 | 工程 3 の直後。確定事項の反映を確かめ、4 へ進むかを決める | 判定結果（`work/PROGRESS.md`） | 未解決ありは m4、なしは m5 へ |
| [m4-qa](m4-qa/SKILL.md) | 工程 4 不足情報のQA | 未解決の確認事項から QA表・開発者検討事項・技術資料依頼を作るとき | `work/qa/QA表_第N回.md`・`.xlsx`（「QA」シート） ほか | m5 へ |
| [m5-means-list](m5-means-list/SKILL.md) | 工程 5 機能の実現手段一覧 | 候補の手段案を比べ、提示用の一覧とレビュー資料を作るとき | `work/means/実現手段一覧.md`、「実現手段一覧」シート、`レビュー資料_第N回.html` | e へ |
| [e-loop-exit](e-loop-exit/SKILL.md) | 判定 E ループの終了判定 | 工程 5 の直後。終了条件を全項目確かめる | 判定結果（`work/PROGRESS.md`） | 終了は m6 へ。しないときは**止まる**（H1 を依頼） |
| [h1-internal-review](h1-internal-review/SKILL.md) | 人間チェック H1 社内レビュー | 人間から社内レビューのフィードバック・承認を受けたとき | `work/qa/社内レビュー記録_第N回.md` ほか | **止まる**（H2 か第N+1回の指示を待つ） |
| [h2-requester-answer](h2-requester-answer/SKILL.md) | 人間チェック H2 要求者の回答 | 要求者の回答・技術資料が `work/input/` に置かれたとき | 確定事項・確認事項・技術参考情報の登録 | **止まる**（第N+1回の指示を待つ） |
| [m6-means-selection](m6-means-selection/SKILL.md) | 工程 6 機能の実現手段の選定 | E でループを終了した後 | `work/means/実現手段選定.md` | 止まらずに m7 へ |
| [m7-arch-spec](m7-arch-spec/SKILL.md) | 工程 7 アーキテクチャ仕様書の作成 | 選定を受けて仕様書を書くとき、H3 の指摘で版を上げるとき | `work/spec/アーキテクチャ仕様書.md`・`.docx`、`トレースリスト.md` | **止まる**（H3 を依頼） |
| [h3-spec-review](h3-spec-review/SKILL.md) | 人間チェック H3 仕様書のレビュー | 人間から仕様書レビューの指摘・承認を受けたとき | `work/spec/仕様書レビュー記録_第N版.md` ほか | 承認で工程 7 の完了（ゴール）。指摘は**止まる** |

- 人間チェック（H1〜H3）の skill は、人間から結果を受けた後の記録と対応を行う。結果・回答・承認を Claude が推測で埋めることはしない。
- 試行（プログラム化）は、H3 の承認の後も、人間の指示なしに着手しない。

## 同梱の書式（templates/）

提出物の書式は、使う skill の `templates/` に置く。

| skill | 書式 | 使い道 |
|---|---|---|
| m4-qa、m5-means-list | `templates/QAシート_参考フォーマット.xlsx` | 提示資料 `QA表_第N回.xlsx` の元 |
| m7-arch-spec | 使う人が `templates/` に置いた docx のテンプレート（今回は `サニー技研_仕様書テンプレート.docx`） | 提出用の `アーキテクチャ仕様書.docx` の表紙・ヘッダ・フッタ・目次・見出しの書式・改訂履歴 |

### M7 の docx のテンプレート

**M7 で使う docx のテンプレートは、`m7-arch-spec/templates/` の下に置く。置き場はここだけとする。** どのテンプレートを使うかは、この skill を使う人が決める。
今回は `m7-arch-spec/templates/サニー技研_仕様書テンプレート.docx` を使う。

- `templates/` に docx が無いと、m7-arch-spec は M7 を始めずに止まり、使うテンプレートを置くよう利用者に依頼する。Claude がテンプレートを選んだり作ったりはしない。
- 2 つ以上あると、どれを使うかを利用者に確かめる。

別のテンプレートに替える場合:

1. 使うテンプレートを `m7-arch-spec/templates/` に置き、使わないものは外す（ファイル名は問わない。`build_spec_docx.py` は `--template` を省くと、そこにある 1 つの docx を使う）。
2. 表紙の題名・副題・日付・文書番号の置き換え元の文字列がテンプレートで違う場合は、`work/spec/spec_docx_config.json` の `placeholders` で合わせる（ひな形は `work/tools/spec_docx_config.example.json`）。
3. docx を作り、PDF で全ページの見た目を確かめる（m7-arch-spec の手順 7 の 4）。

注意: `build_spec_docx.py` は、今回のテンプレートの作りに合わせて書いてある。表紙と改訂履歴の位置を示すスタイル、改訂履歴の表（1 列目の見出しが「版」）、
表のスタイル `見出し付きテーブル(横/青)`、見出し 1・2 のスタイル、目次の TOC フィールドを使う。
これらを持たないテンプレートでは docx を作れないため、そのときはツールを合わせて直す必要がある（直すのは原本の側）。
