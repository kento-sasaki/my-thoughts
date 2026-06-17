# Button コンポーネントの中で Text コンポーネントを使わない

## 規約

- `Button` 内の文字スタイルは `Button` の `variant` / `colorScheme` / `size` で管理する。`Text` で個別調整しない。

```tsx
// 👎 Text で独自にスタイル調整 → デザインシステムの定義に則さない Button になる
<Button><Text textStyle="S1rem_W400_H1.5rem_LS0">Submit</Text></Button>

// 👍 Button の API で表現する
<Button size="2.5rem">Submit</Button>
```

`Text` を使わないと Figma 通りにならないなら、その Figma が誤っているか、`Button` の定義側を直すべき。デザインシステムの定義に則った `Button` を保つ。
