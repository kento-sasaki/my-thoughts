# ESLint

## 導入したいルール

### `react/jsx-curly-brace-presence`

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-brace-presence.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-brace-presence.md)
- 余計な `{}` をエラーにする

```tsx
// NG
<Stack borderColor={'#888'}>

</Stack>

// OK
<Stack borderColor="#888">

</Stack>
```

### `react/self-closing-comp`

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)
- 子コンポーネントを持たないコンポーネントが自己完結していないとエラーにする

```tsx
// NG
<Button icon={<Arrow></Arrow>}></Button>

// OK
<Button icon={<Arrow />} />
```

### `react/jsx-boolean-value`

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)
- JSX の省略形を使わないとエラーにする

```tsx
// NG
<Button disabled={true} />

// OK
<Button disabled />
```

### `react-hooks/exhaustive-deps`

- useEffect, useCallback, useMemo などの依存をチェックしてくれる

### `react/hook-use-state`

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/hook-use-state.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/hook-use-state.md)
- `useState` で管理する state と setter の命名を矯正してくれる
  - `[state, setState]` の形になっていないとエラーにする

```tsx
// NG
const [count, updateCount] = useState(0)

// OK
const [count, setCount] = useState(0)
```

### `no-restricted-imports`

- https://eslint.org/docs/latest/rules/no-restricted-imports
- 指定したモジュールを import した場合にエラーにする

**eslintrc.js**

```js:eslintrc.js
module.exports = {
  rules: {
    'no-restricted-imports': [
      'error',
      {
        paths: [
          {
            name: '@chakra-ui/react',
            importNames: ['Text', 'TextProps'],
          },
        ],
      },
    ],
  },
}
```

**エラーになる**

```tsx
// NG
import { Text } from '@chakra-ui/react'
```

## 参考

[クリーンな React コードを書くための ESLint を調べた](https://zenn.dev/meijin/articles/clean-react-code-eslint-research)
