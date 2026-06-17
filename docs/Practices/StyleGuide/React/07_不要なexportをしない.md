# 不要な export をしない

## 規約

- export する必要のない変数・関数・型・コンポーネントは export しない。

```tsx
// 👍 この画面でしか使わないものは export しない
export const FooView: FC<Props> = () => (<Stack><FooHeader /><FooContent /></Stack>)
const FooHeader: FC = () => (<Stack>{/* ... */}</Stack>)
const FooContent: FC = () => (<Stack>{/* ... */}</Stack>)
```

ESLint の未使用検知を設定していても、`export` していると「どこかで使われているかも」とみなされ検知をすり抜ける。公開範囲を絞ることで、死んだコードを検知できる状態を保つ。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)
