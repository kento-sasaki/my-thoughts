# 自由入力のテキストは zod で前後の空白文字を trim() する

## 規約

- フォームなどでユーザが自由入力する `string` は、zod の `trim()` で前後の半角・全角スペースを取り除く。

```ts
// 👎
memo: z.string().min(1).max(MAX_MEMO_LENGTH)

// 👍
memo: z.string().trim().min(1).max(MAX_MEMO_LENGTH)
```

半角・全角スペースのみの文字列を送信されるのを防ぐため。`min(1)` の前に `trim()` を挟むことで、空白だけの入力が弾かれる。
