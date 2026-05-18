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
