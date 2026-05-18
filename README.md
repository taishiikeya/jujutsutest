# Jujutsu でリポジトリ作成 → GitHub に push する流れ

以下は、**新規プロジェクトを作って GitHub に初回 push する**までの最小手順です。

## 1. ローカルでリポジトリ作成

```bash
mkdir my-project
cd my-project
jj git init --colocate
```

## 2. ファイル作成・編集

```bash
echo "# my-project" > README.md
```

## 3. 変更に説明（コミットメッセージ相当）を付ける

`jj` は description なしだと push を拒否する設定があるため、先に付けます。

```bash
jj describe -m "Initial commit"
```

## 4. main ブックマークを現在のコミットに向ける

初回作成時:

```bash
jj bookmark create main -r @
```

すでに `main` がある場合:

```bash
jj bookmark move main --to @
```

## 5. GitHub 側に空リポジトリを作成

GitHub で `my-project` を作成（README や .gitignore は未作成の空リポジトリ推奨）。

## 6. リモート登録

```bash
jj git remote add origin git@github.com:<USER>/my-project.git
```

HTTPS の場合:

```bash
jj git remote add origin https://github.com/<USER>/my-project.git
```

## 7. push

```bash
jj git push -b main
```

---

## 変更を加えて GitHub に上げる流れ（2回目以降）

既存リポジトリで変更した内容を push する基本手順です。

## 1. 変更を作る

```bash
# 例
echo "some update" >> README.md
```

## 2. 変更内容を確認

```bash
jj status
jj diff
```

## 3. 説明を付ける（コミットメッセージ相当）

```bash
jj describe -m "README を更新"
```

## 4. main ブックマークを現在のコミットに向ける

```bash
jj bookmark move main --to @
```

## 5. push

```bash
jj git push -b main
```

---

## よくあるエラー

### `Bookmark already exists: main`

```bash
jj bookmark move main --to @
jj git push -b main
```

### `Won't push commit ... since it has no description`

```bash
jj describe -m "Initial commit"
jj git push -b main
```

### `refusing to delete the current branch: refs/heads/main`

`main` が `000000000000`（削除状態）を指している時に起きます。

```bash
jj bookmark move main --to @
jj git push -b main
```

---

## `jj new` と `jj edit` の違い・作業の切り替え

### `jj edit <rev/bookmark>`

既存のコミット（または bookmark）に移動して、その地点を編集します。

```bash
jj edit main
jj edit feature-x
```

### `jj new -m "..."`

現在地点（または指定した基点）から新しい作業コミットを作って移動します。  
`-m` は新規コミットの description です。

```bash
jj new -m "test1de"
```

`jj new` だけでは bookmark 名は作られないため、名前で移動したい場合は作成します。

```bash
jj bookmark create test1de -r @
jj edit test1de
```

### 特定のチェンジから枝分かれできるか

できます。`jj new <rev> -m "..."` を使います。

```bash
jj new abcd1234 -m "new work from that change"
```

### `jj edit test1` で `Revision ... doesn't exist` が出る場合

`test1` という rev/bookmark が存在しないのが原因です。まず確認します。

```bash
jj bookmark list
jj log -r 'bookmarks()'
```

リモートだけにある場合（例: `test1@origin`）は:

```bash
jj git fetch
jj edit 'test1@origin'
```

---

## 今いる作業を「別ブランチ」として push する方法

元の `main` とは別に push したい場合は、今のコミット `@` に新しい bookmark を作って、その bookmark を push します。

```bash
# 例: 現在の作業を test1de というブランチ名で push
jj bookmark create test1de -r @
jj git push -b test1de
```

すでに同名 bookmark がある場合は move してから push:

```bash
jj bookmark move test1de --to @
jj git push -b test1de
```
