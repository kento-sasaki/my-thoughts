# mockGraphQLQuery と useMutation にジェネリクスを指定する

## 規約

- `mockGraphQLQuery` / `mockGraphQLMutation` はジェネリクスで型を指定し、型チェックを効かせる。
- `useMutation` も Mutation と Variables のジェネリクスを必ず指定する。

```tsx
// 👍 ジェネリクスで型を指定
mockGraphQLQuery<PatientProfileCheckQuery>('FindPatientProfileCheckQuery', standard)
const [createPacingPlan] = useMutation<CreatePacingPlan, CreatePacingPlanVariables>(CREATE_PACING_PLAN)
```

型を指定しないと、モックデータや変数がスキーマと乖離していてもコンパイル時に検出できない。指定すれば、スキーマ変更時に影響箇所を自動で検出できる。
