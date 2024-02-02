# useState で前の値を正しく更新する

## 前提

```tsx
const [counter, setCounter] = useState(0)

const handleIncrement = useCallback(() => {
  setCounter(counter + 1)
}, [counter])
```

## 問題点

- `useCallback` の deps に `counter` が含まれてしまい、余計なレンダリングが走る可能性がある

## 改善

`setCounter` にコールバック関数を渡し、その引数で前の値を取得する

```tsx
const [counter, setCounter] = useState(0)

const handleIncrement = useCallback(() => {
  setCounter((prev) => prev + 1)
}, []) // 余計な deps がなくなる
```

## 参考
