---
title: mockGraphQLQuery と useMutation にジェネリクスを指定する
status: draft
created: 2026-06-17
updated: 2026-06-22
---

# mockGraphQLQuery と useMutation にジェネリクスを指定する

## 概要

- `mockGraphQLQuery` や `mockGraphQLMutation` を使う際は、必ずジェネリクスで型を指定して型チェックが効くようにする。
- `useMutation` を使う際も、Mutation と Variables のジェネリクスを必ず指定する。

## 👎 不適切な例

```tsx
// mockGraphQLQuery に型を指定していない
mockGraphQLQuery('FindUserProfileQuery', standard)

// useMutation に型を指定していない
const [createTodo] = useMutation(CREATE_TODO)
```

## 👍 適切な例

```tsx
// mockGraphQLQuery にジェネリクスで型を指定
mockGraphQLQuery<UserProfileQuery>(
  'FindUserProfileQuery',
  standard
)

// useMutation にジェネリクスで型を指定
const [createTodo] = useMutation<
  CreateTodo,
  CreateTodoVariables
>(CREATE_TODO)
```

## なぜこのルールが必要か

ジェネリクスを指定しないと、モックデータや変数の型チェックが効かず、実際の GraphQL スキーマと乖離したデータを渡してもコンパイル時に検出できない。型を指定しておけば、スキーマ変更時に影響箇所を自動的に検出でき、モックと本番コードの両方が型の安全網に守られる。

## 例外

特になし
