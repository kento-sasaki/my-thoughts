# nullable な値を boolean にする際は !! を使う

## 規約

- nullable な値を boolean として扱う場合、`!!` で明示的に boolean に変換する。
- ただし `0` や `""` を有効値として扱いたい場合は `value !== undefined && value !== null` を使う。

```ts
// 👎 boolean | null | undefined のまま
const permitted = currentPatient.patient?.isPermittedForExposure

// 👍 boolean に変換
const permitted = !!currentPatient.patient?.isPermittedForExposure

// 0 や "" を有効値として扱いたい場合
const hasValue = value !== undefined && value !== null
```

`boolean | null | undefined` のまま条件分岐で使うと、意図しない挙動が起きうる。`!!` で型を明確にし、意図を伝える。
