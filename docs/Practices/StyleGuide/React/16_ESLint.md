---
title: ESLint
status: draft
created: 2026-06-17
updated: 2026-06-19
---

# ESLint

## 概要

- コード規約のうち機械的に判定できるものは、ESLint で強制する。
- 採用したいルールの例:
  - **`react/jsx-curly-brace-presence`** — 余計な `{}` をエラーに。`borderColor={'#888'}` → `borderColor="#888"`。
  - **`react/self-closing-comp`** — 子を持たない要素は自己終了に。`<Arrow></Arrow>` → `<Arrow />`。
  - **`react/jsx-boolean-value`** — boolean props は省略形に。`disabled={true}` → `disabled`。
  - **`react-hooks/exhaustive-deps`** — `useEffect` / `useCallback` / `useMemo` の依存をチェック。
  - **`react/hook-use-state`** — `useState` の命名を `[state, setState]` 形に矯正。
  - **`no-restricted-imports`** — 禁止 import をエラーに（例：`@chakra-ui/react` の `Text` を禁止）。
  - **`eslint-plugin-import`** — `import` の順序を整列。

## なぜこのルールが必要か

規約を「読ませる」のではなく「実行可能な制約」に落とすことで、人間も AI も自己検証・自己修正できる。レビューや暗黙知に頼らず、規約違反をコミット前・PR 前に機械的に検出できるため、規約の遵守がスケールする。

→ なぜ：[ドキュメントより実行可能な制約へ](../../../Principles/AIWorkflow/02_実行可能な制約へ.md)

## 例外

特になし

## 参考

- [クリーンな React コードを書くための ESLint を調べた（zenn / meijin）](https://zenn.dev/meijin/articles/clean-react-code-eslint-research)
