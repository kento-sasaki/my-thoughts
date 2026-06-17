# useEffect は関連ロジックをまとめる

## 規約

- 同じ条件分岐やフローに属する処理は、個別の `useEffect` に分けず1つにまとめる。

```tsx
// 👍 関連する処理を1つの useEffect に。else if で優先順位が明示される
useEffect(() => {
  if (!programOpened) { showError('E18'); navigate(home, { replace: true }) }
  else if (!previousSectionCompleted) { showError('E19'); navigate(home, { replace: true }) }
}, [programOpened, previousSectionCompleted])
```

まとめることで条件の優先順位が明確になり、不要なレンダリングが減り、関連ロジックが1箇所に集まって保守性が上がる。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)
