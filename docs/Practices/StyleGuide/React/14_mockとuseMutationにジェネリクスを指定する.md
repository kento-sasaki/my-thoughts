<!-- TODO: Review -->

# mockGraphQLQuery と useMutation にジェネリクスを指定する

## 概要

- `mockGraphQLQuery` や `mockGraphQLMutation` を使う際は、必ずジェネリクスで型を指定して型チェックが効くようにする。
- `useMutation` を使う際も、Mutation と Variables のジェネリクスを必ず指定する。

## 👎 不適切な例

```tsx
// mockGraphQLQuery に型を指定していない
mockGraphQLQuery('FindPatientProfileCheckQuery', standard)

// useMutation に型を指定していない
const [createPacingPlan] = useMutation(CREATE_PACING_PLAN)
```

## 👍 適切な例

```tsx
// mockGraphQLQuery にジェネリクスで型を指定
mockGraphQLQuery<PatientProfileCheckQuery>(
  'FindPatientProfileCheckQuery',
  standard
)

// useMutation にジェネリクスで型を指定
const [createPacingPlan] = useMutation<
  CreatePacingPlan,
  CreatePacingPlanVariables
>(CREATE_PACING_PLAN)
```

## なぜこのルールが必要か

ジェネリクスを指定しないと、モックデータや変数の型チェックが効かず、実際の GraphQL スキーマと乖離したデータを渡してもコンパイル時に検出できない。型を指定しておけば、スキーマ変更時に影響箇所を自動的に検出でき、モックと本番コードの両方が型の安全網に守られる。

## 例外

特になし
