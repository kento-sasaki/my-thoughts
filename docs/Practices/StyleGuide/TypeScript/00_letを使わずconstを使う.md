<!-- TODO: Review -->

# let を使わず const を使う

## 概要

- 再代入が必要に見えても、式や三項演算子で初期値を組み立てて `const` で宣言する。
- 値の意味が変わる段階は、再代入せず別の名前を与える。

## 👎 不適切な例

```ts
// let で何度も再代入 → どの時点の max を参照しているか分かりにくく、型も number に絞られない
let max = d3.max(...)
if (max && targetValue) max = Math.max(max, targetValue)
const isThereMax = !!max
max = isNumber(max) ? Math.floor(max + 3000) : 10000
```

## 👍 適切な例

```ts
// const で組み立て、意味が変わる段階は別名にする
const maxStep = targetValue ? d3.max(..., targetValue) : d3.max(...)
const maxRange = isNumber(maxStep) ? Math.floor(maxStep + 3000) : 10000
```

## なぜこのルールが必要か

`let` で再代入すると「いまどの時点の値か」を読み手が追う必要があり、認知負荷が上がる。別の変数に値を退避しているなら、それは意味的に別の変数＝別の名前にすべきというサインである。`const` で宣言すれば再代入されないことが保証され、型も絞り込みやすくなる。

## 例外

特になし。
