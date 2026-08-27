## Exercise 2a

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
