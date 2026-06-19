<!-- TODO: Review -->

# React.Fragment で全体をラップしたコンポーネントを作らない

## 概要

- コンポーネント全体を `React.Fragment`（`<> ... </>`）でラップして返さない。`<Stack>` など自前の器で囲み、自分の見た目を自分で完結させる。
- ただし、これが許容できるケースもある（下記の例外）。

## 👎 不適切な例

```tsx
const FooForm = () => (
  <>
    <FormControl>
      <Input
        type="email"
        placeholder="メールアドレス"
        onChange={handleChange}
      />
    </FormControl>
    <FormControl>
      <Input
        type="password"
        placeholder="パスワード"
        onChange={handleChange}
      />
    </FormControl>
    <Button>Submit</Button>
  </>
)
```

## 👍 適切な例

```tsx
const FooForm = () => (
  <Stack>
    <FormControl>
      <Input
        type="email"
        placeholder="メールアドレス"
        onChange={handleChange}
      />
    </FormControl>
    <FormControl>
      <Input
        type="password"
        placeholder="パスワード"
        onChange={handleChange}
      />
    </FormControl>
    <Button>Submit</Button>
  </Stack>
)
```

なお、コンポーネント「全体」をラップしているのでなければ、`React.Fragment` の使用自体は問題ない。

```tsx
<Stack gap={2}>
  {options.map((_, i) => (
    // コンポーネント全体をラップしていなければ問題ない
    <React.Fragment key={i}>
      <Text>Foo</Text>
      <Button>Bar</Button>
    </React.Fragment>
  ))}
</Stack>
```

## なぜこのルールが必要か

全体を `<> ... </>` でラップしたコンポーネントを作ると、このコンポーネントを呼ぶ親の影響を受ける可能性がある。
例えば前者のコンポーネントが `FooForm` だったとすると、以下のように呼び出したときに `FooForm` が意図しない影響を受けてしまう。

```tsx
<Stack gap={4}>
  <FooForm />
</Stack>
```

このような呼び方をした場合、`FooForm` の中の `FormControl` と `Button` が親で設定している `gap={4}` の影響を受けてしまう。
親側からすると、`FooForm` の実装を知っていて適切な gap を指定しなければいけなくなり、`FooForm` からすると、自身を呼ぶ親コンポーネントは gap を適切に設定している前提で実装していることになる。
いずれにせよこれは親と子に余計な依存が生じている実装になっている。自前の器で囲めば、自分の見た目を自分で完結させられる。

→ なぜ：[最小依存性：親に依存しないコンポーネント](../../../Principles/Engineering/01_最小依存性.md)

## 例外

### Page の場合

Page は基本的に上から更に `Stack` でラップされるなどがありえないので問題ない。
また、`Metadata` のような表示されないコンポーネントだけを含んで `<> ... </>` でラップしている場合も問題ない。

```tsx
<>
  <Metadata title="ログイン" description="ログイン" />
  <PatientLoginView />
</>
```

### モーダルなどを表示する場合

モーダルのような、viewport に対して表示位置が決まるコンポーネントは上から `Stack` などでラップしても影響がないので問題ない。

```tsx
<>
  <FooView />
  <BarModal open={open} />
</>
```
