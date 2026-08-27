# Exercise 2a

## Task1 TODOをブロックする

### TODOをブロックしたhooks

```sh
#!/bin/bash
# Blocks commits containing hardcoded secrets in staged files
# ステージングファイルにTODOが含まれる場合にコミットをブロック

PATTERNS="TODO"

if git diff --cached --name-only | xargs grep -iE "$PATTERNS" 2>/dev/null; then
  echo ""
  echo "ERROR: Possible secret found in staged files."
  echo "エラー：ステージングファイルにTODOが見つかりました。"
  echo ""
  echo "1. Remove it from your code / コードから削除する"
  echo "2. Then commit again / その後コミットする"
  echo ""
  exit 1
fi

exit 0
```

### ブロックされるかテスト

```sh
$ echo "TODO:fix later" > hoge.txt && git add . && git commit -m "add: todo txt"

TODO:fix later

ERROR: Possible secret found in staged files.
エラー：ステージングファイルにTODOが見つかりました。

1. Remove it from your code / コードから削除する
2. Then commit again / その後コミットする
```

### 綺麗なファイルにした結果

```sh
$ git commit -m "fix: deleted TODO"

[exercise-2a/ki-mutaku 8c02137] fix: deleted TODO
 1 file changed, 1 insertion(+)
 create mode 100644 hoge.txt
```

## Task2 メッセージの長さルール

### hooks

```sh
#!/bin/bash
# Enforces: feat|fix|docs|chore|test: description
# 形式を強制: feat|fix|docs|chore|test: 説明

MSG=$(cat "$1")
PATTERN="^(feat|fix|docs|chore|test|refactor|style): .{5,}"
LENGTH=$(echo -n "$MSG" | wc -m)
MAX=50

if ! echo "$MSG" | grep -qE "$PATTERN" || [ "$LENGTH" -gt "$MAX" ] ; then
  echo ""
  echo "ERROR: Commit message format is wrong."
  echo "エラー：コミットメッセージの形式が間違っているか、文字数がオーバーしています。"
  echo ""
  echo "Required format / 必要な形式:"
  echo "  feat: add login page"
  echo "  fix: resolve null pointer in auth"
  echo "  docs: update README"
  echo ""
  echo "文字数制限は50文字以内です。"
  echo ""
  echo "Your message was / あなたのメッセージ: \"$MSG\""
  echo ""
  exit 1
fi

exit 0
```

### ブロックされるか確認

```sh
ERROR: Commit message format is wrong.
エラー：コミットメッセージの形式が間違っているか、文字数がオーバーしています。

Required format / 必要な形式:
  feat: add login page
  fix: resolve null pointer in auth
  docs: update README

文字数制限は50文字以内です。

Your message was / あなたのメッセージ: "fix: add hoge to hoge.txt but this word has no mean, so it will be fixed"
```

### 正しいメッセージにした結果

```sh
$ git commit -m "fix: add hoge"
[exercise-2a/ki-mutaku 8e22b55] fix: add hoge
 2 files changed, 49 insertions(+), 1 deletion(-)
 create mode 100644 ANSWERS.md
```

## Task3 先生のように説明

### exit 0とexit 1の意味

`exit 0`とは、プログラムが正常に終了したことを呼び出し元のプロセスに伝えるための命令コードです。`pre-commit`や`commit-msg`の末尾にこれを書いているのは、ファイル内容やコミットメッセージが規約に違反することはなかったとプロセスに伝えるためです。

一方で、`exit 1`とは何らかのエラーが発生したことを呼出元プロセスに知らせるためのコードです。実際、`pre-commit`や`commit-msg`で条件分岐に引っかかったとき、エラーだと判定したため、エラーメッセージを出すとともに、プログラム側にもエラーを伝えています。
