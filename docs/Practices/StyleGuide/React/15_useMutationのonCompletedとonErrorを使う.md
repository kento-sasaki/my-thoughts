---
title: useMutation の onCompleted と onError を活用する
status: draft
created: 2026-06-17
updated: 2026-06-19
---

# useMutation の onCompleted と onError を活用する

## 概要

- `useMutation` の結果に応じた処理（エラートースト表示、モーダルを閉じるなど）は、`onCompleted` と `onError` コールバックを使う。

## 👎 不適切な例

```tsx
// mutation の成功/失敗にかかわらず後続処理が実行される
const handleSubmit = async () => {
  await createPacingPlan({ variables: { input } })
  // 通信エラー時もここが実行され、画面が壊れる
  closeModal()
  setSectionIndex((prev) => prev + 1)
}
```

## 👍 適切な例

```tsx
const [createPacingPlan] = useMutation<
  CreatePacingPlan,
  CreatePacingPlanVariables
>(CREATE_PACING_PLAN, {
  onCompleted: () => {
    closeModal()
  },
  onError: (error) => {
    toaster.create({
      title: 'エラーが発生しました',
      description: error.message,
      type: 'error',
    })
  },
})
```

## なぜこのルールが必要か

mutation の成功/失敗にかかわらず後続処理（画面遷移、state 更新など）を実行すると、通信エラー時にも後続処理が走り、画面が壊れる原因になる。`onCompleted` / `onError` を使えば、成功時・失敗時の処理を明確に分離でき、エラーハンドリングが確実になる。

## 例外

特になし
