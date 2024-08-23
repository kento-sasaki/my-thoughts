# React のコンポーネントをどのように設計するべきか

## これは何？

React のコンポーネント設計についての方針を記述したドキュメント。
開発者間での共通認識を持つことを目的とする。

## 概要

- 理想のコンポーネントは、再利用しやすく、理解しやすく、独立しているコンポーネントである
- 理想のコンポーネントのためには 1 つのコンポーネントの責任が大きすぎないことが重要
  - どのように責任を分割していくかは後述
- 具体的な方法は [するべきこと](#-するべきこと) と [しないべきこと](#-しないべきこと) を参照

## 考える観点

React のコンポーネント設計を考える際に様々な観点が存在するが、このドキュメントでは以下の観点を中心にコンポーネント設計の方針を考える。

- どんなコンポーネントが理想か
  - 理想のコンポーネントの状態を前提に、それを実現するための設計方針を考える
- コンポーネントの粒度
- どのコンポーネントにどの責任を持たせるか
  - ロジック
  - UI
    - 余白
    - 背景
  - 状態

## 考えない観点

逆にこのドキュメントでは以下の観点については考えない。

- コードの細かい書き方
  - 命名規則、型の書き方、etc...
- ディレクトリ構成
- パフォーマンスの観点
  - `React.memo`, `useMemo`, `useCallback` etc...

## どんなコンポーネント設計が理想か

コンポーネントが以下のような状態であることを理想とし、以降ではこの状態を実現するための設計方針を考える。

- 再利用性が高いこと: 他のコンポーネントで再利用しやすい
- 可読性が高いこと: コンポーネントの見通しがよく、理解しやすい
- 独立性が高いこと: 他のコンポーネントに依存せず、独立して動作できる

また、これら 3 つの状態はお互いに関連している部分も多く、基本的に"単一責任の原則"に従ってコンポーネントを設計し、適切な粒度で分割することで概ね実現できる。
とはいえ、実際には厳密にコンポーネントが 1 つの責任だけ果たすように実装すると、コンポーネントの数が増えすぎると考えられる。そのため単一とは言わないまでも、理解しやすい粒度で責任を分割し、その粒度毎にコンポーネントを実装する。

## コンポーネントの適切な粒度

前述の通り、1 つのコンポーネントが持つ責任を理解しやすいようにすることを前提としつつ、まずは React のコンポーネントが持ちうる責任を考えると主に以下のものがある。

- **ロジック**
  - フォームのバリデーション
  - コンポーネントの disabled or enabled
  - コンポーネントの visible or invisible
  - 画面の制御（どの画面に遷移するか、どのコンポーネントを表示するか、etc...）
- **UI**
  - 色、文字、余白、レイアウト、幅、高さ、etc...
- **API リクエスト**
  - query, mutation の実行
    - 結果のハンドリング
    - input の変換

また、Redwood には大きく 3 種類のコンポーネント(Page, Cell, Component)があり、これらがそれぞれ持つべき責任は以下のようになる。
**※ 非常にややこしいが、このドキュメントでは `コンポーネント` は React のコンポーネント全般を指し、`Component` は Redwood における Page でも Cell でもないコンポーネントを指す。**

### Page

Page が持つべき責任は主に以下の通り。
特に、専任事項については、Page だけが持つべき責任である。

- **専任事項**
  - path parameter を受け取る
  - query parameter を受け取る
  - Cell を呼ぶ
    - query param を受け取るのは Page のみなので、Cell を呼ぶのも Page のみであるのが理想
    - 基本は上記の通りだが、query を実行するタイミングが異なる場合、Cell をネストすることは許容する（[例外](#例外)を参照）
- **Page に向いている責任**
  - レイアウト
    - 子コンポーネント同士の余白
    - 画面端からの余白
    - 画面全体の背景色
  - 子コンポーネントで共通して参照する状態の管理
  - 子コンポーネントの制御
    - state, path params, query params に応じて、表示するコンポーネントを制御する

### Cell

Cell が持つべき責任は主に以下の通り。
特に、専任事項については、Cell だけが持つべき責任である。

- **専任事項**
  - query の実行とハンドリング
- **Cell に向いている責任**
  - mutation の実行とハンドリング
    - mutation を実行したあとに query を再実行したりするので、Cell に持たせると都合がいいケースが多い
  - mutation のための input の変換

また、Cell は基本的に Page に呼ばれるため、コンポーネントツリーの中で比較的上位にあることが多いが、必ずしも Cell が他の子コンポーネントの親になる必要はない。
可能な限り取得した値を参照する範囲だけが Cell 以下のコンポーネントで描く範囲であることが望ましい。
例えば、以下のような画面を考えると...、

<img src="./images/Example.png" width="200px">

この画面で対象の医療機関を検索して結果を表示したい場合、Cell の責任範囲は医療機関の一覧より下の部分であれば良い。
ヘッダー、タブ、検索フォームなどは Cell よりも親のコンポーネント(Page)が持つべきである。

### Component(Page でも Cell でもないコンポーネント)

Component が持つべき責任は主に以下の通り。

- **専任事項**
  - フォームのバリデーション
  - コンポーネントの disabled or enabled
  - コンポーネントの visible or invisible
  - その他静的な要素

各種コンポーネントの責任範囲は以下のようになる。
![責任範囲](./images/ComponentResponsibility.png)

### 例: `NewProAnswer`

※ 実際の `NewProAnswer` とは異なります。

**`NewProAnswerPage.tsx`**(一部省略しています)

```tsx:NewProAnswerPage.tsx
const NewProAnswerPage = () => {
  return (
    {/* 責任: 子コンポーネントに共通する背景色を設定する */}
    <Box bgColor="blue.50" minH="100vh">
      <NewProAnswerCell answerAt={answerAtDate} />
    </Box>
  )
}

export default NewProAnswerPage
```

**`NewProAnswerCell.tsx`**(一部省略しています)

```tsx:NewProAnswerCell.tsx
// 責任: 入力された値を mutation の input に変換する
const toProAnswerHoge = (/* 入力された値 */) => {
  return { /* 変換後の値 */ }
}

// 責任: query の実行とハンドリング
export const Success = ({ currentPatient, answerAt }: Props) => {
  const { recentDaysWhenBloodPressureRecorded } = currentPatient

    // 責任: mutation の実行とハンドリング
  const [createAnswer] = useMutation< /* 型 */ >(CREATE_ANSWER_MUTATION, {
    onCompleted: (data) => { /* 成功時の処理 */ },
    onError: (error) => { /* 失敗時の処理 */ },
  })

  // 責任: 入力値を mutation の input に変換し、mutation を実行する
  const onFinish = async (state) => {
    await createAnswer({
      variables: {
        input: { /* 変換後の値 */ },
      },
    })
  }

  return (
    <NewProAnswerScreen
      answerAt={answerAt}
      recentDaysWhenBloodPressureRecorded={
        recentDaysWhenBloodPressureRecorded || []
      }
      onFinish={onFinish}
    />
  )
}
```

**`NewProAnswerScreen.tsx`** (一部省略しています)

```tsx:NewProAnswerScreen.tsx
export const NewProAnswerScreen = ({
  answerAt,
  recentDaysWhenBloodPressureRecorded,
  onFinish,
}: Props): NonNullable<ReactNode> => {
  // 責任: 問診の進捗、回答内容を管理する
  const [state, dispatch] = useNewProAnswerReducer()

  // 責任: 画面の制御
  const onBack = () => {
    dispatch({ type: ACTION_TYPE_BACK })
  }

  // この画面だけは Header と ProgressBar がないので、ここで表示する。
  // 本当はこれだけ別の Page にするべきなのかもしれない。
  if (state.currentStep === 'start') {
    return (
      <StartForm onSubmit={() => dispatch({ type: ACTION_TYPE_INITIALIZE })} />
    )
  }

  return (
    <Stack>
      <ProHeader onBack={onBack} />

      <Stack px={4} py={2} spacing={6}>
        <ProgressBar value={state.progress} />

        {(() => {
          switch (state.currentStep) {
            case 'vital':
              return (
                <VitalForm
                  state={state.vital}
                  progress={state.progress}
                  recentDaysWhenBloodPressureRecorded={
                    recentDaysWhenBloodPressureRecorded
                  }
                  onSubmit={() => {
                    dispatch({/* アクション */})
                  }}
                  onBack={onBack}
                />
              )
            case 'pastBloodPressure':
              return (
                <PastBloodPressureForm
                  state={state.pastBloodPressure}
                  progress={state.progress}
                  recentDaysWhenBloodPressureRecorded={
                    recentDaysWhenBloodPressureRecorded
                  }
                  onSubmit={() => {
                    dispatch({/* アクション */})
                  }}
                  onBack={onBack}
                />
              )

            // ... 他にもいくつかのステップが続く
          }
        })()}
      </Stack>
    </Stack>
  )
}
```

## 👍 するべきこと

ここまでの方針を踏まえ、より具体的にするべきことをいくつか挙げる。

### 共通のコンポーネントは親で持つ

`NewProAnswer` の例でも見たように、Header や ProgressBar などが子コンポーネントで共通している場合、それらの親が持つべきである。

### 共通の余白は親コンポーネントで設定する

コンポーネントと同様、余白も共通のものは親コンポーネントで設定するべきである。特に画面端からの余白はその画面内で一定であることがほとんどなので、一番外側の大きなコンポーネントで設定するのが理想である。

### Fragment Collocation を活かす

Cell が取得した値を子コンポーネントに渡していき、子がその値を使う場合、Fragment Collocation を使って子コンポーネント側で必要な情報を定義しておくべきである。
例えば、患者一覧を `PatientsCell` で取得してそれぞれの患者情報をカード形のコンポーネント `PatientCard` で表示するようなケースを考える。

患者情報の query を Cell に書くと以下のようになる。

<details>
<summary>Fragment Collocation を使わない実装</summary>

**`PatientsCell.tsx`**

```tsx
const QUERY = gql`
  query PatientsQuery {
    patients {
      id
      familyName
      firstName
      birthDate
      gender
    }
  }
`

export const Success = ({ patients }: CellSuccessProps<PatientsQuery>) => {
  return (
    <Stack spacing={2}>
      {patients.map((patient) => (
        <PatientCard key={patient.id} patient={patient} />
      ))}
    </Stack>
  )
}
```

そして `PatientCard` では、Cell から受け取る props として、Cell の query から定義された型を使う必要がある。

**`PatientCard.tsx`**

```tsx
type Props = {
  patient: PatientsQuery['patients'][0]
}

export const PatientCard: FC<Props> = ({ patient }) => {
  return (
    <Box>
      {/* 患者情報を表示 */}
    </Box>
  )
```

</details>

この形だと、`PatientCard` 側は `PatientCell` の型に依存していることになり、自分を呼ぶのが `PatientCell` であることを前提とした実装になっている。
そのため、以下のように Fragment Collocation を使って `PatientCard` 側で必要な情報を定義しておき、`PatientCell` ではその情報を取得する query を実行する。

<details>
<summary>Fragment Collocation を使った実装</summary>

**`PatientsCell.tsx`**

```tsx
const QUERY = gql`
  query PatientsQuery {
    patients {
      ...PatientCardFragment
    }
  }
`

export const Success = ({ patients }: CellSuccessProps<PatientsQuery>) => {
  return (
    <Stack spacing={2}>
      {patients.map((patient) => (
        <PatientCard key={patient.id} patient={patient} />
      ))}
    </Stack>
  )
}
```

**`PatientCard.tsx`**

```tsx
registerFragment(gql`
  fragment PatientCardFragment on Patient {
    id
    familyName
    firstName
    birthDate
    gender
  }
`)

type Props = {
  patient: PatientCardFragment
}

export const PatientCard: FC<Props> = ({ patient }) => {
  return (
    <Box>
      {/* 患者情報を表示 */}
    </Box>
  )
```

</details>

## 👎 しないべきこと

### margin をコンポーネントに持たせるべきではない

コンポーネントが margin を持つと、そのコンポーネントを使う親側では margin があることを知っている必要がある。
これによって子コンポーネントの独立性、再利用性が下がってしまう。

### 再利用しやすいコンポーネントを作るべきだが、不必要な再利用しすぎるべきではない

複数の画面で共通しているコンポーネントを再利用しやすい形で実装することは重要だが、不必要に複数のコンポーネントで再利用することは避けるべきである。
`NewProAnswer` の例のような、複数の画面で共通するヘッダーがある場合、それらを各コンポーネント(`XxxForm` のコンポーネント)で繰り返し再利用するのではなく、共通の親がヘッダーを持つべきである。
Redwood の場合、複数の Page 間で共通のコンポーネントは `Set` の `wrap` を使って持たせることもできるので、これも検討する。

### 余計なネストを作るべきではない

関数の if 文が深くなればなるほど認知負荷が高くなるのと同様、ネストが深いコンポーネントほど認知負荷が高い。
Box, Stack, Flex などのコンポーネントは余計なネストが発生しがちなので、減らせるネストは見逃さず、常に削減できるようにする。

```tsx
// NG
<Box bg="blue.50">
  <Stack p={4} spacing={3}>
    {/* 子コンポーネントたち */}
  </Stack>
</Box>

// OK
<Stack bg="blue.50" p={4} spacing={3}>
  {/* 子コンポーネントたち */}
</Stack>
```

### Cell をネスト、または並列に配置するべきではない

Page が Cell を呼ぶ際に、その Cell がまた別の Cell を呼ぶことや、Page が複数の Cell を呼ぶことは避けるべきである。

<detail>
<summary>Cell のネストの例</summary>
例えば以下のような、`Page` が `ProAnswersCell` を呼び、`ProAnswersCell` が `CopingActionsCell` を呼ぶような構造があるとする。

**Page**

```tsx
const Page = () => {
  return <ProAnswersCell />
}
```

**ProAnswersCell**

```tsx
const QUERY = gql`
  query FindProAnswers {
    currentPatient {
      proAnswers {
        id
      }
    }
  }
`

export const Success = (props) => {
  return <CopingActionsCell {...props} />
}
```

**CopingActionsCell**

```tsx
const QUERY = gql`
  query FindCopingActions {
    currentPatient {
      copingActions {
        id
      }
    }
  }
`

export const Success = (props) => {
  return <Component {...props} />
}
```

この場合、2 つの Cell は 1 つに統一し、Page がそれを呼ぶようにするべきである。

**Page**

```tsx
const Page = () => {
  return <ProAnswersAndCopingActionsCell /> // この命名は適切ではないが、例示のため
}
```

**ProAnswersAndCopingActionsCell**

```tsx
const QUERY = gql`
  query FindProAnswers {
    currentPatient {
      proAnswers {
        id
      }
      copingActions {
        id
      }
    }
  }
`

export const Success = (props) => {
  return <Component {...props} />
}
```

</detail>

<detail>
<summary>Cell の並列</summary>
以下のように `Page` が `ProAnswersCell` と `CopingActionsCell` を並列に呼ぶような構造も避けるべきである。

```tsx
const Page = () => {
  return (
    <>
      <ProAnswersCell />
      <CopingActionsCell />
    </>
  )
}
```

この場合でも Cell がネストしている例と同様に、1 つの Cell に統一するべきである。
</detail>

#### 例外

query を実行するタイミングが異なる場合、Cell をネストすることは許容する。
例えば、以下の画面のようなケースでは許容される。

この画面ではフォームの途中でモーダルが表示されており、Page 直下の query とは別の query で取得した値をこのモーダルで表示する必要がある。このような場合は Page 直下の Cell と統一することは難しいため、このような例外を許容する。

<img src="./images/NewCopingAction.png" width="200px">

### 画面描画を伴う値の取得で useQuery は使うべきではない

query を実行するのは基本的に Cell の責任であるため、useQuery は基本的には必要ない。
useQuery を使うとすれば、画面の描画を伴わない query の場合である。
例: `web/src/hooks/usePushNotification.ts`
