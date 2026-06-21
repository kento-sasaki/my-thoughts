---
title: Button コンポーネントの中で Text コンポーネントを使わない
status: published
created: 2026-06-17
updated: 2026-06-21
---
# Button コンポーネントの中で Text コンポーネントを使わない

## 概要

- `Button` 内で表示する文字のスタイリングは `Button` の `variant` / `colorScheme` / `size` などで管理する。`Text` で個別に調整しない。

## 👎 不適切な例

```tsx
// Text で独自にスタイル調整 → デザインシステムの定義に則さない Button になる
<Button>
  <Text textStyle="Std-16N-175">Submit</Text>
</Button>
```

## 👍 適切な例

```tsx
// Button の API で表現する
<Button size="lg">Submit</Button>
```

## なぜこのルールが必要か

`Button` はデザインシステムの定義に従っているべきであり、`Button` の中の文字は `variant` / `colorScheme` / `size` で表現できるように実装されているはずである。`Text` を使って独自に文字のスタイルを変更するのは、定義に則さない `Button` を作ることになる。`Text` を使わないと Figma の通りにならないのであれば、その Figma が誤っているか、`Button` の定義側を修正する必要がある。

## 例外

特になし
