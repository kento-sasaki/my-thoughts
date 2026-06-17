# let を使わず const を使う

## 規約

- 再代入が必要に見えても、式や三項演算子で初期値を組み立てて `const` で宣言する。意味の変わる値には別の名前を与える。

```ts
// 👎 let で何度も再代入 → どの時点の max を参照しているか分かりにくく、型も number に絞られない
let max = d3.max(...)
if (max && targetValue) max = Math.max(max, targetValue)
const isThereMax = !!max
max = isNumber(max) ? Math.floor(max + 3000) : 10000

// 👍 const で組み立て、意味が変わる段階は別名にする
const maxStep = targetValue ? d3.max(..., targetValue) : d3.max(...)
const maxRange = isNumber(maxStep) ? Math.floor(maxStep + 3000) : 10000
```

`let` で再代入すると「どの時点の値か」を読み手が追う必要があり、別の変数に退避しているなら、それは意味的に別の変数＝別の名前にすべきというサイン。`const` なら型も絞り込みやすい。
