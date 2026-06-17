# React.Fragment で全体をラップしたコンポーネントを作らない

## 規約

- コンポーネント全体を `<>...</>`（Fragment）でラップして返さない。`<Stack>` など自前の器で囲む。
- ただし許容できるケースもある（下記の例外）。

```tsx
// 👎 全体を Fragment で返すと、親の gap などの影響を受ける
const FooForm = () => (<><FormControl>...</FormControl><Button>Submit</Button></>)

// 👍 自前の器で囲み、自分の見た目を自分で完結させる
const FooForm = () => (<Stack><FormControl>...</FormControl><Button>Submit</Button></Stack>)
```

全体を Fragment で返すと、`<Stack gap={4}>` の中に置いたとき内部が親の `gap` に左右され、親子に暗黙の依存が生まれる。

## 例外

- Page（上から `Stack` でラップされ得ない）や、`Metadata` のような非表示要素のみを含む場合。
- モーダルなど viewport 基準で表示位置が決まるコンポーネント。

→ なぜ：[最小依存性：親に依存しないコンポーネント](../../../Principles/Engineering/01_最小依存性.md)
