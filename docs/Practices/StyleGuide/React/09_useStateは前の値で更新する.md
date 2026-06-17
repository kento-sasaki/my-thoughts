# useState で前の値を正しく更新する

## 規約

- `useState` の状態を更新するとき、現在の状態を直接参照しない。新しい状態を計算する関数を渡す（`setHoge((prev) => ...)`）。

```tsx
// 👎 counter を直接参照 → deps に counter が必要、複数回呼ぶと意図通りに動かない
const increment = useCallback(() => setCounter(counter + 1), [counter])

// 👍 前の値をコールバックの引数で取得 → 余計な deps が消える
const increment = useCallback(() => setCounter((prev) => prev + 1), [])
```

直接参照すると `useCallback` の deps に状態が入って余計なレンダリングが走り、`increment()` を連続実行するケースで意図通りに動かない。
