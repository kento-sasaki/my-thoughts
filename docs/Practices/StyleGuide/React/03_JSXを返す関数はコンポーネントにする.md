# React.JSX.Element を返す関数を定義しない

## 規約

- `React.JSX.Element` を返す関数は、関数として定義せずコンポーネントとして定義する。
- パスカルケース＋名詞で命名し、`<Component />` の形で呼ぶ。

```tsx
// 👎 キャメルケースの関数に見えるが、返り値は JSX.Element（実態はコンポーネント）
const getCircle = (target: Date) => { /* ... return <Circle /> */ }

// 👍 コンポーネントとして定義する
const Circle: FC<{ target: Date }> = ({ target }) => { /* ... */ }
```

見た目では関数に見え、関数コンポーネントであることがわかりにくいコードを避ける。

→ なぜ：[命名は言語](../../../Principles/Engineering/02_命名は言語.md)
