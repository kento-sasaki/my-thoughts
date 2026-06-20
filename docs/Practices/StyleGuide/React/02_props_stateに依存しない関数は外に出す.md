---
title: props や state に依存しない関数・変数はコンポーネントの外に書く
status: draft
created: 2026-06-17
updated: 2026-06-19
---

# props や state に依存しない関数・変数はコンポーネントの外に書く

## 概要

- `props` や `state` に依存しない関数・変数は、コンポーネントの外で定義する。
- 「この関数は本当にこのコンポーネントの状態に依存しているか？」を問い、していないものは外に追い出す。

## 👎 不適切な例

コンポーネントがレンダリングされるたびに `handleClick` が再生成される。

```tsx
const MyComponent: FC<{ name: string }> = ({ name }) => {
  // この関数は props や state に依存していない
  const handleClick = () => {
    console.log('Button clicked!')
  }

  return (
    <div>
      <p>Hello, {name}!</p>
      {/*
      MyComponent が再レンダリングされると handleClick も再生成されるため、
      ChildComponent も再レンダリングされる
      */}
      <ChildComponent onClick={handleClick} />
    </div>
  )
}

// ChildComponent は memo化しているが、
// handleClick が再生成されるので再レンダリングを防げない
const ChildComponent = memo<{onClick: () => void}>(({ onClick }) => {
  return (
    // UI の実装
  )
})
```

## 👍 適切な例

`handleClick` 関数をコンポーネントの外で定義することで、関数の再生成を防ぐ。

```tsx
// props や state に依存しないため、コンポーネントの外で定義
const handleClick = () => {
  console.log('Button clicked!')
}

const MyComponent: FC<{ name: string }> = ({ name }) => {
  return (
    <div>
      <p>Hello, {name}!</p>
      {/*
      MyComponent が再レンダリングされても handleClick は再生成されないため、
      ChildComponent の再レンダリングは防ぐことができる
      */}
      <ChildComponent onClick={handleClick} />
    </div>
  )
}

const ChildComponent = memo<{onClick: () => void}>(({ onClick }) => {
  return (
    // UI の実装
  )
})
```

## なぜこのルールが必要か

コンポーネントがレンダリングされるたびに関数やオブジェクトが再生成されるのを防ぐためである。
関数を props や state に依存しないにもかかわらずコンポーネント内で定義すると、再レンダリングのたびに新しい参照が生成され、`memo` 化した子コンポーネントであっても props の参照が変わったとみなされ再描画されてしまう。依存していない関数・変数を外に出すことで、こうした無駄な再生成・再描画を避けられる。

→ なぜ：[最小依存性：親に依存しないコンポーネント](../../../Principles/Engineering/01_最小依存性.md)

## 例外

特になし

## 参考

- https://medium.com/@jburnwal5/how-can-you-optimize-performance-in-your-react-application-made-with-functional-components-f33fa9c66628
- https://www.codementor.io/blog/react-optimization-5wiwjnf9hj
