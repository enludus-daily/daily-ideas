# daily-ideas

enludus-daily の「毎日1本の iOS アプリ」の材料と記録。

- **アイデア**：困りごとやアイデアは [Issue](../../issues/new/choose) の「アイデア・困りごと」から（ラベル `idea`）。公開なので個人情報は書かない。
- **その日の記録**：毎日 22:00（日本時間）に自動のセッションが、課題を選び、アプリを作り、TestFlight まで進める。経過と結果は、その日の Issue（ラベル `daily`）に書く。
- **枠の台帳**：`slots.json`（Bundle ID `com.enludus.daily.001〜007`）。空きがなくなったら、本人が App Store Connect で App を足し、ここに追記する。

## アプリのリポジトリの作成（`.github/workflows/create-app-repo.yml`）

毎日のセッションは、組織向けの API（リポジトリの作成）を呼べない。そこで、セッションはこのワークフローを `workflow_dispatch` で起動するだけにし、作成は Actions が `daily-app-template` から行う。

- トークン：このリポジトリの Secrets の `DAILY_REPO_CREATOR_TOKEN` にだけ置く（組織の Secrets にはしない。ほかのアプリのリポジトリから使えないようにするため）。セッションはトークンを持たない。
- トークンの権限（fine-grained、組織 enludus-daily、All repositories）：Administration = Read and write、Contents = Read、Metadata = Read。
- 入力の検査：名前は `app-<今日の YYYYMMDD>-<英小文字・数字・ハイフン>` だけ。1日2回まで、連続2回の失敗で止まる（daily-actions@v1 の guard）。
- Issue のイベントでは起動しない（public で誰でも Issue を書けるため）。
- ガードは daily-actions を SHA で固定して使う（ワークフロー内の `ref:`）。

## push の前の検査

`git config core.hooksPath .githooks` で、push の前に `scripts/scan-sensitive.sh`（daily-actions/scan/ の複製）が走る。

## ラベル（`labels.json`）

| ラベル | 意味 |
|---|---|
| `idea` / `used` / `ai-found` | アイデア / 材料に使った / セッションが自分で見つけた |
| `daily` | その日の作業の記録 |
| `building` → `ready-for-review` | 作っている → TestFlight まで済み、本人の確認と提出待ち |
| `needs-human` | 止まった。本人の判断・操作が要る |
| `done` | 本人が提出した、または終えた |

## 本人がすること

`ready-for-review` の Issue を見て：アプリ名を決める → App Store Connect の仮の名前を変える → TestFlight で確かめる → App プライバシーを申告する → 審査に提出する → `slots.json` を `submitted` にし、Issue に `done` を付ける。
