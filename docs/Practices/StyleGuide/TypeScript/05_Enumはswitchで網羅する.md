# Enum を switch 文で網羅する

## 規約

- Enum や Union 型を分岐するときは `switch` で全ケースを網羅し、`default` で `never` チェックを置いて、ケースの追加漏れをコンパイルエラーにする。

```ts
type Status = 'todo' | 'doing' | 'done'

const label = (status: Status): string => {
  switch (status) {
    case 'todo':  return '未着手'
    case 'doing': return '進行中'
    case 'done':  return '完了'
    default: {
      // Status に新しい値を足すと、ここで型エラーになり対応漏れに気づける
      const _exhaustive: never = status
      return _exhaustive
    }
  }
}
```

`if` の連鎖や暗黙の `default` だと、Enum/Union に値を追加したときの対応漏れが実行時まで気づけない。`never` による網羅チェックで、漏れを型レベルで検知する。

## 参考

- [プログラミングの原則：enum の比較はすべてバグ（Uzabase for Engineers）](https://tech.uzabase.com/entry/2023/12/03/100712)
