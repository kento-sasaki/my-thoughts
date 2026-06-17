# Context 導入時のモック共通化

## 規約

- React Context を導入する場合、Storybook で使うデフォルト値（`defaultContextValue`）を1箇所に共通定義する。
- 各 Story では必要な値だけをオーバーライドする。

```tsx
// hooks/__mocks__/patientHomeContext.ts
export const defaultContextValue: PatientHomeContextValue = { assistantMessage: '', consultations: [], todos: [], records: [] }

// 各 Story は decorator でオーバーライドのみ
decorators: [(Story) => (
  <PatientHomeProvider value={{ ...defaultContextValue, todos: [{ id: '1', title: 'テスト' }] }}><Story /></PatientHomeProvider>
)]
```

Context の型が変わるたびに全 Story を直す負荷をなくし、各 Story では「本当にテストしたい値」だけが明示される（Signal-to-Noise 比の向上）。

## 例外

- Context のフィールドが3つ以下など十分シンプルなら共通化しなくてよい。
