# props や state に依存しない関数・変数はコンポーネントの外に書く

## 規約

- `props` や `state` に依存しない関数・変数は、コンポーネントの外で定義する。

```tsx
// 👎 再レンダリングのたびに handleClick が再生成され、memo 化した子まで再描画される
const MyComponent: FC<{ name: string }> = ({ name }) => {
  const handleClick = () => console.log('clicked')
  return <Child onClick={handleClick} />
}

// 👍 依存していないなら外へ
const handleClick = () => console.log('clicked')
const MyComponent: FC<{ name: string }> = ({ name }) => <Child onClick={handleClick} />
```

「この関数は本当にこのコンポーネントの状態に依存しているか？」を問い、していないものは外に追い出す。

→ なぜ：[最小依存性：親に依存しないコンポーネント](../../../Principles/Engineering/01_最小依存性.md)
