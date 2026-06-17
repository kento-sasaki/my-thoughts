# コンポーネントに margin を持たせない

## 規約

- コンポーネントは自身の外側の `margin` を持たない。要素間の余白は、それらを並べる親（レイアウト）が決める。

```tsx
// 👎 コンポーネント自身が外側 margin を持つ → 置き場所によって余白が破綻する
const Card = () => <Box mt={4}>...</Box>

// 👍 余白は親が Stack の gap などで与える
<Stack gap={4}><Card /><Card /></Stack>
```

コンポーネントが外側 margin を持つと、どこに置くかで余白が変わってしまい、親のレイアウトに暗黙に依存する。余白の責務は親に置く。

→ なぜ：[最小依存性：親に依存しないコンポーネント](../../../Principles/Engineering/01_最小依存性.md)

## 参考

- [margin の責務を考える（zenn / seya）](https://zenn.dev/seya/articles/09545c7503baa4)
