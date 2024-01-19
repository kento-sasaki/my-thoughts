# wip: let を使わず const を使う

以下のようなコードを考える

```tsx
// この max は number | undefined になる
let max = d3.max( 
	// ... 
)

if (max && targeValue) {
  max = Math.max(max, targetValue)
}

const isThereMax = !!max

max = isNumber(max) ? Math.floor(max + 3000) : 10000
```

## 問題点

1. `max` の値が定義した後に変わるので、どの時点の `max` を参照しているのか分かりにくく、読みにくいコードになってしまう。
    1. `const isThereMax = !!max` の部分で、**この時点での** `max` が必要になり別の変数に代入しているが、このようなことをしているということはこの時点での `max` と、これ以降の `max` が意味的には別の変数と言える。ならば別の名前であるべき。
    2. `max = isNumber(max) ? Math.floor(max + 3000) : 10000` の部分で、実際には `max` の型は number に確定するが、TypeScript はこれを推論してくれない。
    
    <aside>
    💡 ここに VScode のスクショ
    
    </aside>
    

## 改善

以下のように `const` を使う

```tsx
// この maxStep は number | undefined になる
const maxStep = targeValue ? d3.max( 
	// ... ,
	targeValue
) : d3.max( 
	// ... ,
)

const isThereMax = !!max

const maxRange = isNumber(maxStep) ? Math.floor(maxStep + 3000) : 10000
```