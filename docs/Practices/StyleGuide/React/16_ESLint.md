# ESLint

コード規約のうち機械的に判定できるものは、ESLint で強制する。導入したいルールの例。

## 採用したいルール

- **`react/jsx-curly-brace-presence`** — 余計な `{}` をエラーに。`borderColor={'#888'}` → `borderColor="#888"`。
- **`react/self-closing-comp`** — 子を持たない要素は自己終了に。`<Arrow></Arrow>` → `<Arrow />`。
- **`react/jsx-boolean-value`** — boolean props は省略形に。`disabled={true}` → `disabled`。
- **`react-hooks/exhaustive-deps`** — `useEffect` / `useCallback` / `useMemo` の依存をチェック。
- **`react/hook-use-state`** — `useState` の命名を `[state, setState]` 形に矯正。
- **`no-restricted-imports`** — 禁止 import をエラーに（例：`@chakra-ui/react` の `Text` を禁止）。
- **`eslint-plugin-import`** — `import` の順序を整列。

規約を「読ませる」のではなく「実行可能な制約」に落とすことで、AI も自己検証・自己修正できる。

→ なぜ：[ドキュメントより実行可能な制約へ](../../../Principles/AIWorkflow/02_実行可能な制約へ.md)

## 参考

- [クリーンな React コードを書くための ESLint を調べた（zenn / meijin）](https://zenn.dev/meijin/articles/clean-react-code-eslint-research)
