<!-- TODO: Review -->

# Enum を switch 文で網羅する

## 概要

- Enum や Union 型を分岐するときは `switch` で全ケースを網羅する。
- `default` で `never` チェックを置き、ケースの追加漏れをコンパイルエラーにする。

## 👎 不適切な例

```ts
type Status = 'todo' | 'doing' | 'done'

// if の連鎖。Status に値を足しても気づけず、暗黙のフォールスルーで実行時まで漏れに気づけない
const label = (status: Status): string => {
  if (status === 'todo') return '未着手'
  if (status === 'doing') return '進行中'
  return '完了' // 'done' 以外が来ても素通りしてしまう
}
```

## 👍 適切な例

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

## なぜこのルールが必要か

`if` の連鎖や暗黙の `default` だと、Enum/Union に値を追加したときの対応漏れが実行時まで気づけない。`switch` で全ケースを列挙し、`default` で `never` 型への代入による網羅チェックを置くことで、ケースの追加漏れを型レベル（コンパイル時）で検知できる。

## 例外

特になし。

## 参考

- [プログラミングの原則：enum の比較はすべてバグ（Uzabase for Engineers）](https://tech.uzabase.com/entry/2023/12/03/100712)
