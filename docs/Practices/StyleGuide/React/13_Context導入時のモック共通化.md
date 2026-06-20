---
title: Context 導入時のモック共通化
status: draft
created: 2026-06-17
updated: 2026-06-19
---

# Context 導入時のモック共通化

## 概要

- React Context を導入する場合、Storybook の Story で使うデフォルト値（`defaultContextValue`）を1箇所に共通定義する。
- 各 Story ではデフォルト値をオーバーライドするだけにする。
- Context の型変更時に全 Story を修正する負荷を最小化する。

## 👎 不適切な例

各 Story ファイルに `createContextValue` を個別に定義している。

```tsx
// AssistantMessageSection.stories.tsx
const createContextValue = (): PatientHomeContextValue => ({
  assistantMessage: 'テストメッセージ',
  consultations: [],
  todos: [],
  records: [],
  // ... 無関係な Section のモック値も全て定義
})

// TodoSection.stories.tsx
const createContextValue = (): PatientHomeContextValue => ({
  assistantMessage: '',
  consultations: [],
  todos: [{ id: '1', title: 'テスト' }],
  records: [],
  // ... ほぼ同じ定義の重複
})
```

## 👍 適切な例

デフォルト値を1箇所に定義し、各 Story ではオーバーライドのみ行う。

```tsx
// hooks/__mocks__/patientHomeContext.ts
export const defaultContextValue: PatientHomeContextValue = {
  assistantMessage: '',
  consultations: [],
  todos: [],
  records: [],
  // ... 全フィールドのデフォルト値を1箇所で定義
}

// AssistantMessageSection.stories.tsx
import { defaultContextValue } from '../hooks/__mocks__/patientHomeContext'

const meta: Meta = {
  decorators: [
    (Story) => (
      <PatientHomeProvider value={{
        ...defaultContextValue,
        assistantMessage: 'テストメッセージ',  // この Story に必要な値だけオーバーライド
      }}>
        <Story />
      </PatientHomeProvider>
    ),
  ],
}

// TodoSection.stories.tsx
import { defaultContextValue } from '../hooks/__mocks__/patientHomeContext'

const meta: Meta = {
  decorators: [
    (Story) => (
      <PatientHomeProvider value={{
        ...defaultContextValue,
        todos: [{ id: '1', title: 'テスト' }],  // この Story に必要な値だけオーバーライド
      }}>
        <Story />
      </PatientHomeProvider>
    ),
  ],
}
```

## なぜこのルールが必要か

Context の型が変わるたびに、全ての Story の `createContextValue` を更新する必要がなくなる。各 Story では「本当にテストしたい値」だけが明示されるため、Signal-to-Noise 比が向上する。`AssistantMessageSection` の Story を書くために `TodoSection` のモック値を考える必要がなくなり、Story の関心がそのコンポーネントに集中する。

## 例外

- Context のフィールド数が3つ以下など、十分にシンプルな場合は共通化しなくてもよい。
