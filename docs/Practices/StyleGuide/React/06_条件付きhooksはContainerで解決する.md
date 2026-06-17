# 条件付き hooks は Container パターンで解決する

## 規約

- Hooks を条件分岐の中で呼ばない（React のルール）。
- 条件によって必要な hooks が異なる場合は、Container コンポーネントで分岐し、各コンポーネントは自身に必要な hooks だけを呼ぶ。

```tsx
// 👍 Container が条件を裁き、各コンポーネントは必要な hooks だけ呼ぶ
const DailyRecordContainer: FC<Props> = ({ isShamPatient, ...props }) =>
  isShamPatient ? <ShamDailyRecord {...props} /> : <DailyRecord {...props} />
```

条件分岐で hooks を呼ぶと呼び出し順が変わり state が崩れる。不要な hooks を呼ぶと不要なデータ取得・副作用が走る。

## 例外

- 違いが hooks ではなく表示内容だけなら、Container は不要。通常の条件分岐でよい。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)
