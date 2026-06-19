<!-- TODO: Review -->

# useState で前の値を正しく更新する

## 概要

- `useState` で管理している状態を更新するとき、現在の状態を直接参照しない。
- `setHoge((prev) => prev + 1)` など、新しい状態を計算する関数を渡す。

## 👎 不適切な例

```tsx
const [counter, setCounter] = useState(0)

// counter を直接参照 → deps に counter が必要
const increment = useCallback(() => {
  setCounter(counter + 1)
}, [counter])

const incrementDouble = () => {
  increment()
  increment()
  // 現在値 +2 したいが、この実装だとできない
}
```

## 👍 適切な例

`setCounter` にコールバック関数を渡し、その引数で前の値を取得する。

```tsx
const [counter, setCounter] = useState(0)

// 前の値をコールバックの引数で取得 → 余計な deps が消える
const increment = useCallback(() => {
  setCounter((prev) => prev + 1)
}, [])
```

## なぜこのルールが必要か

現在の状態を直接参照すると、`useCallback` の deps に `counter` が含まれてしまい、余計なレンダリングが走る可能性がある。さらに、例のように `increment` を連続して2回実行するケースなどで意図通りに動作しない。

## 例外

特になし

## 参考

- https://zenn.dev/stin/articles/use-appropriate-api
