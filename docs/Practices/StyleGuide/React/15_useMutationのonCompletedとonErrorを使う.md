# useMutation の onCompleted と onError を活用する

## 規約

- mutation の結果に応じた処理（モーダルを閉じる、エラートースト表示など）は、`onCompleted` / `onError` コールバックで行う。

```tsx
// 👎 成功/失敗にかかわらず後続処理が走り、通信エラー時に画面が壊れる
await createPacingPlan({ variables: { input } })
closeModal(); setSectionIndex((prev) => prev + 1)

// 👍 成功時・失敗時を分離する
const [createPacingPlan] = useMutation<CreatePacingPlan, CreatePacingPlanVariables>(CREATE_PACING_PLAN, {
  onCompleted: () => closeModal(),
  onError: (error) => toaster.create({ title: 'エラーが発生しました', description: error.message, type: 'error' }),
})
```

成功/失敗にかかわらず後続処理（画面遷移・state 更新）を実行すると、通信エラー時に画面が壊れる。コールバックで分離すればエラーハンドリングが確実になる。
