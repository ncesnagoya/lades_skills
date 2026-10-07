# skill 一覧

本ファイルは、`.claude/skills/` に置いた skill の目的と使う順番を示す。
各 skill がどの場面で適用されるかの正は、各 `SKILL.md` の `description` にある。
本ファイルに適用場面を書き写してはならない。
理由: 2 か所に同じ記述を持つと、片方だけを直した場合に記述が食い違う。

Claude Code は `.claude/skills/<名前>/SKILL.md` だけを skill として読み込む。
本ファイルは skill として読み込まれない。

## 工程の順番に沿って使う skill

次の表は、仕様設計書を作る工程の順番に並べてある。

| 順 | skill | 目的 | 主な入力 | 主な出力 |
|---|---|---|---|---|
| 1 | [analyze-requirements](analyze-requirements/SKILL.md) | 要求仕様を解析し、機能候補と論点を導く | `work/input/` の要求仕様(.xlsx) | `work/functions/analysis/` の解析ノート |
| 2 | [build-qa-sheet](build-qa-sheet/SKILL.md) | 解析ノートの論点を QA として起票し、提出用の QAシートを作る | 解析ノートの `outputs/`、社内レビューの記入済み .xlsx | `work/qa/items/`、`work/qa/QAリスト.md`、`work/qa/QAシート.xlsx` |
| 3 | [build-function-list](build-function-list/SKILL.md) | 機能候補から確定版の機能リストと機能 doc を作る | 解析ノートの `outputs/` | `work/functions/機能リスト.md`、`work/functions/items/` |
| 4 | [build-trace-list](build-trace-list/SKILL.md) | 機能から要求へのトレースリストを作る | 機能リスト、解析ノートの `outputs/` | `work/functions/トレースリスト.md`、`work/functions/トレース判断.md` |
| 5 | [create-spec](create-spec/SKILL.md) | 機能 doc から仕様設計書を作る | 機能 doc、QA doc | `work/spec/` の仕様一覧・本文・トレースリスト・.docx |

QA の回答が返った場合、工程 1 から工程 4 を必要な範囲でやり直す。
仕様設計書の着手条件は [work/CLAUDE.md](../../work/CLAUDE.md) 「作業フローの規律」の 4 にある。

## 工程を問わず使う skill

| skill | 目的 |
|---|---|
| [verify-against-source](verify-against-source/SKILL.md) | 生成物の内容を要求仕様の原本と照合する |
| [session-end](session-end/SKILL.md) | セッションの終了時に記録を作り、記録の欠落を確認し、git へ反映する |

## skill ではないフォルダ

| フォルダ | 内容 |
|---|---|
| [workspace-notes](workspace-notes/workspace-notes/SKILL.md) | このワークスペースの運用方針を集めた注記である。`SKILL.md` が 1 階層深い位置にあるため、Claude Code は skill として読み込まない |
