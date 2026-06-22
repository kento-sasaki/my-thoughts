---
title: useEffect は関連ロジックをまとめる
status: draft
created: 2026-06-17
updated: 2026-06-22
---

# useEffect は関連ロジックをまとめる

## 概要

- 同じ条件分岐やフローに属する処理は、個別の `useEffect` に分けず1つの `useEffect` にまとめる。

## 👎 不適切な例

```tsx
// 関連する処理が複数の useEffect に分散
useEffect(() => {
  if (!hasAccess) {
    showError('ACCESS_DENIED')
    navigate(routes.home(), { replace: true })
  }
}, [hasAccess])

useEffect(() => {
  if (!previousStepCompleted) {
    showError('STEP_INCOMPLETE')
    navigate(routes.home(), { replace: true })
  }
}, [previousStepCompleted])
```

## 👍 適切な例

```tsx
// 関連する処理を1つの useEffect に。else if で優先順位が明示される
useEffect(() => {
  if (!hasAccess) {
    showError('ACCESS_DENIED')
    navigate(routes.home(), { replace: true })
  } else if (!previousStepCompleted) {
    showError('STEP_INCOMPLETE')
    navigate(routes.home(), { replace: true })
  }
}, [hasAccess, previousStepCompleted])
```

## なぜこのルールが必要か

まとめることで条件の優先順位が明確になる。また、不要なレンダリング回数を減らせるうえ、関連するロジックが1箇所に集まることで保守性が向上する。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)

## 例外

特になし
