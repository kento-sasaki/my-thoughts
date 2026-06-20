---
title: 不要な export をしない
status: draft
created: 2026-06-17
updated: 2026-06-19
---

# 不要な export をしない

## 概要

- export する必要のない変数・関数・型・コンポーネントは export しない。

## 👎 不適切な例

```tsx
export const FooView: FC<Props> = () => {
  return (
    <Stack>
      <FooHeader />
      <FooContent />
    </Stack>
  )
}

// この画面でしか使っていない FooHeader を export する必要はない
export const FooHeader: FC = () => {
  return (
    <Stack>
      {/* UI の実装 */}
    </Stack>
  )
}

// この画面でしか使っていない FooContent を export する必要はない
export const FooContent: FC = () => {
  return (
    <Stack>
      {/* UI の実装 */}
    </Stack>
  )
}
```

## 👍 適切な例

```tsx
export const FooView: FC<Props> = () => {
  return (
    <Stack>
      <FooHeader />
      <FooContent />
    </Stack>
  )
}

// 不要な export はしない
const FooHeader: FC = () => {
  return (
    <Stack>
      {/* UI の実装 */}
    </Stack>
  )
}

// 不要な export はしない
const FooContent: FC = () => {
  return (
    <Stack>
      {/* UI の実装 */}
    </Stack>
  )
}
```

## なぜこのルールが必要か

ESLint で未使用の変数などをチェックするルールを設定していても、`export` していると「どこかで使われているかも」とみなされ、実際には不要になっていても検知をすり抜けてしまう。公開範囲を絞ることで、死んだコードを検知できる状態を保つ。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)

## 例外

特になし
