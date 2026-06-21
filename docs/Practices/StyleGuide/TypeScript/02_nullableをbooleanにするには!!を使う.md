---
title: nullable な値を boolean にする際は !! を使う
status: draft
created: 2026-06-17
updated: 2026-06-19
---
# nullable な値を boolean にする際は !! を使う

## 概要

- nullable な値を boolean として扱う場合、`!!` を使って明示的に boolean 型に変換する。
- ただし `0` や `""` を有効な値として扱いたい場合は `value !== undefined && value !== null` を使う。

## 👎 不適切な例

```ts
// permitted の型が boolean | null | undefined のまま
const permitted = currentPatient.patient?.isPermittedForExposure

// 0 が falsy として無効扱いされてしまう
const hasValue = !!value
```

## 👍 適切な例

```ts
// permitted の型が boolean になる
const permitted = !!currentPatient.patient?.isPermittedForExposure

// 0 や "" を有効な値として扱いたい場合
const hasValue = value !== undefined && value !== null
```

## なぜこのルールが必要か

`boolean | null | undefined` のまま条件分岐で使うと、意図しない挙動が起きうる。`!!` で明示的に boolean に変換することで型が明確になり、意図が伝わりやすくなる。ただし `!!` は `0` や `""` も falsy として扱うため、それらを有効値とみなしたい場合は `value !== undefined && value !== null` で `null`/`undefined` だけを除外する。

## 例外

特になし。
