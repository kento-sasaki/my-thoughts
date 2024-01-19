# ESLint

## 導入したいルール

### **`react/jsx-curly-brace-presence`**

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-brace-presence.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-curly-brace-presence.md)
- 余計な `{}` をエラーにする
- `<Stack borderColor={’#888’}> ... </Stack>` → `<Stack borderColor="#888"> ... </Stack>`

### **`react/self-closing-comp`**

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)
- 子コンポーネントを持たないコンポーネントが自己完結していないとエラーにする

### **`react/jsx-boolean-value`**

- [https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md](https://github.com/jsx-eslint/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)
- JSX の省略形を使わないとエラーにする
- `<Button disabled={true} />` → `<Button disabled />`

### `react-hooks/exhaustive-deps`

- useEffect, useCallback, useMemo などの依存をチェックしてくれる

## 参考

[クリーンなReactコードを書くためのESLintを調べた](https://zenn.dev/meijin/articles/clean-react-code-eslint-research)