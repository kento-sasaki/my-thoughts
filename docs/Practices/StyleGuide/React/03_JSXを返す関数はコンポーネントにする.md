<!-- TODO: Review -->

# React.JSX.Element を返す関数を定義しない

## 概要

- `React.JSX.Element` を返す関数は、関数として定義せずコンポーネントとして定義する。
- パスカルケース + 名詞の形で命名し、呼び出す側では `<Component />` という形で呼ぶ。

## 👎 不適切な例

```tsx
const getCircle = (target: Date) => {
  if (isAllClear(target)) {
    return <CheckBoldCircle />
  } else if (isClear(target)) {
    return <HyphenCircle />
  } else {
    return <DefaultCircle />
  }
}
```

## 👍 適切な例

```tsx
const Circle: FC<{ target: Date }> = ({ target }) => {
  if (isAllClear(target)) {
    return <CheckBoldCircle />
  } else if (isClear(target)) {
    return <HyphenCircle />
  } else {
    return <DefaultCircle />
  }
}
```

## なぜこのルールが必要か

前者の例のコードはキャメルケースで命名された関数のような見た目だが、返り値は `React.JSX.Element` なので、実際には関数コンポーネントと同じである。見た目では関数に見え、関数コンポーネントであることがわかりにくいコードになっている。
パスカルケース + 名詞でコンポーネントとして定義し `<Component />` の形で呼ぶことで、それが関数コンポーネントであることが一目で伝わり、命名と実態が一致する。

→ なぜ：[命名は言語](../../../Principles/Engineering/02_命名は言語.md)

## 例外

特になし
