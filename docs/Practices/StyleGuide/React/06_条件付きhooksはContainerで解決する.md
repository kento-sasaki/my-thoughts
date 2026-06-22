---
title: 条件付き hooks は Container パターンで解決する
status: draft
created: 2026-06-17
updated: 2026-06-22
---

# 条件付き hooks は Container パターンで解決する

## 概要

- React Hooks を条件分岐の中で呼び出してはならない（React のルール）。
- 条件によって異なる hooks が必要な場合は、Container コンポーネントで分岐する。
- Container が条件を判断し、それぞれの条件に対応したコンポーネントを返す。各コンポーネントは自身に必要な hooks だけを呼ぶ。

## 👎 不適切な例

条件分岐の中で hooks を呼び出している、または条件によって不要な hooks も呼んでいる。

```tsx
// NG: 条件分岐の中で hooks を呼ぶ (React のルール違反)
const Dashboard: FC<Props> = ({ isGuest }) => {
  if (!isGuest) {
    const { data } = useProfileQuery()  // Error
  }
  // ...
}
```

```tsx
// NG: 条件によって不要な hooks も全て呼んでいる
const Dashboard: FC<Props> = ({ isGuest }) => {
  // ゲストでは不要だが、hooks はトップレベルに書かないといけないので呼ぶ
  const { data: profileData } = useProfileQuery()
  const { data: activityData } = useActivityQuery()

  if (isGuest) {
    // profileData, activityData は使わない
    return <GuestDashboardView />
  }

  return <DashboardView profileData={profileData} activityData={activityData} />
}
```

## 👍 適切な例

Container コンポーネントで条件分岐し、各コンポーネントは自身に必要な hooks だけを呼ぶ。

```tsx
// Container: 条件を判断して適切なコンポーネントを返す
const DashboardContainer: FC<Props> = ({ isGuest, ...props }) => {
  if (isGuest) {
    return <GuestDashboard {...props} />
  }
  return <Dashboard {...props} />
}

// 登録ユーザー用: 必要な hooks を呼ぶ
const Dashboard: FC<Props> = (props) => {
  const { data: profileData } = useProfileQuery()
  const { data: activityData } = useActivityQuery()
  return <DashboardView profileData={profileData} activityData={activityData} />
}

// ゲスト用: 不要な hooks は呼ばない
const GuestDashboard: FC<Props> = (props) => {
  return <GuestDashboardView />
}
```

## なぜこのルールが必要か

React Hooks は呼び出し順序に依存して state を管理しているため、条件分岐の中で hooks を呼ぶと呼び出し順序が変わり、state の対応が崩れる。
また、不要な hooks を呼ぶことは、不要なデータ取得や副作用を引き起こす可能性がある。
Container パターンにより条件を Container 側で裁き、各コンポーネントは必要な hooks だけを呼ぶようにすることで、各コンポーネントの責務が明確になり、テストも容易になる。

→ なぜ：[制約が設計を導く](../../../Principles/Engineering/03_制約が設計を導く.md)

## 例外

- 条件による違いが hooks ではなく、表示内容だけの場合は Container パターンは不要。通常の条件分岐で対応する。

```tsx
// OK: hooks は共通で、表示内容だけが異なる場合
const Dashboard: FC<Props> = ({ isGuest }) => {
  const { data } = useCommonQuery() // 両方で使う hooks

  return isGuest ? <GuestView data={data} /> : <MemberView data={data} />
}
```
