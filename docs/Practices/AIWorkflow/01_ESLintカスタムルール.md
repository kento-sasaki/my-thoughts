<!-- TODO: Review -->

# 規約を ESLint カスタムルールにする

## 規約はドキュメントに書くだけでは守られない

「Button の中で Text コンポーネントを使わない」という規約がある。理由は明快で、Button の文字スタイルは `variant` や `size` で管理する設計になっているのに、内部で Text を直接スタイルすると、その設計の外側に出てしまうからだ。

この規約を Markdown に書いてレビュー指摘の根拠にする、というやり方はしばらく機能する。しかし規模が大きくなるほど、例外が増え始める。「急いでいたので」「Figma の指定に合わせるため」という例外が重なり、いつしかドキュメントの規約と実態が乖離していく。これは人間チームが抱える古典的な問題だ。

AI が書き手に加わると、この問題は別の形で現れる。CLAUDE.md や規約集を読ませた直後は効く。ところが会話が進むほど、最初に読ませたドキュメントは薄まっていく。モデルは直近のコンテキストを優先する傾向があるため、序盤に渡した規約は後半になると弱くなる。また、長い規約集はそれだけでコンテキストを圧迫する。「読ませる文章」として渡す限り、この構造的な弱点は変わらない。

発想を変える必要がある。機械的に判定できる規約は、文章ではなく**実行可能な制約**にしてしまえばいい。それが ESLint カスタムルールだ。対応する原則は[ドキュメントより実行可能な制約へ](../../Principles/AIWorkflow/02_実行可能な制約へ.md)に書いた。

## ESLint カスタムルールの基本構造

ESLint カスタムルールは、plugin にルールを登録し、`create(context)` 関数の中で AST ノードを検査する構造をとる。最小構成はこうなる。

```js
// plugin/index.js
const noTextInsideButton = require('./rules/no-text-inside-button')

module.exports = {
  rules: {
    'no-text-inside-button': noTextInsideButton,
  },
}
```

```js
// plugin/rules/no-text-inside-button.js
module.exports = {
  meta: {
    type: 'problem',
    docs: {
      description: 'Button コンポーネントの子孫に Text コンポーネントを使わない',
    },
    schema: [],
  },
  create(context) {
    // ノードに入ったとき・出たときに呼ばれるリスナーをオブジェクトで返す
    return {
      // JSXOpeningElement にマッチするたびに呼ばれる
      JSXOpeningElement(node) {
        // ...
      },
    }
  },
}
```

`create` が返すオブジェクトのキーは AST ノードのセレクタだ。ESLint はソースコードを AST に変換し、ノードを走査しながらマッチするリスナーを呼ぶ。`context.report()` でエラーを報告する。

## ルールを書く — `no-text-inside-button`

「Button の中で Text を使わない」を実装する。検出したいのは次のパターンだ。

👎
```tsx
<Button><Text textStyle="S1rem_W400_H1.5rem_LS0">送信</Text></Button>
```

👍
```tsx
<Button>送信</Button>
```

JSX の AST では `<Button>...</Button>` が `JSXElement` ノードとなり、その `openingElement` に `JSXOpeningElement`（`name.name === "Button"`）が入る。子要素は `children` 配列に並ぶ。`<Text>` は `JSXElement` であり、その `openingElement.name.name` が `"Text"` になる。

子孫を再帰的に探索するには、スタックを使って「Button の中にいる」状態を追う。

```js
// plugin/rules/no-text-inside-button.js
module.exports = {
  meta: {
    type: 'problem',
    docs: {
      description: 'Button コンポーネントの子孫に Text コンポーネントを使わない',
    },
    schema: [],
    messages: {
      noTextInsideButton:
        'Button の中で Text を使わないでください。文字スタイルは Button の props で管理します。',
    },
  },
  create(context) {
    // Button の中にいるかどうかを追うスタック
    const buttonStack = []

    return {
      JSXOpeningElement(node) {
        if (node.name.type === 'JSXIdentifier' && node.name.name === 'Button') {
          buttonStack.push(node)
        }
      },
      'JSXOpeningElement:exit'(node) {
        if (node.name.type === 'JSXIdentifier' && node.name.name === 'Button') {
          buttonStack.pop()
        }
      },
      JSXElement(node) {
        if (buttonStack.length === 0) return
        const opening = node.openingElement
        if (
          opening.name.type === 'JSXIdentifier' &&
          opening.name.name === 'Text'
        ) {
          context.report({
            node,
            messageId: 'noTextInsideButton',
          })
        }
      },
    }
  },
}
```

`JSXOpeningElement` で Button に入るたびスタックに積み、`JSXOpeningElement:exit` で抜けるたびに取り出す。スタックが空でないとき（Button の中にいるとき）に `Text` の JSXElement を見つけたらエラーを報告する。ネストした Button も正しく扱える。

## テストを書く — RuleTester で自己文書化する

ESLint の `RuleTester` を使うと、ルールの期待動作をテストとして書ける。テストは仕様書でもある。「何を通して何を弾くか」が一覧で見えるため、ルールの意図が新しいメンバーにも伝わりやすい。

```js
// plugin/rules/no-text-inside-button.test.js
const { RuleTester } = require('eslint')
const rule = require('./no-text-inside-button')

const ruleTester = new RuleTester({
  languageOptions: {
    ecmaVersion: 2020,
    sourceType: 'module',
    parserOptions: {
      ecmaFeatures: { jsx: true },
    },
  },
})

ruleTester.run('no-text-inside-button', rule, {
  valid: [
    // Text なし: 通る
    { code: '<Button>送信</Button>' },
    // Button の外の Text: 通る
    { code: '<div><Text>ラベル</Text><Button>送信</Button></div>' },
    // Button の中の Text でない要素: 通る
    { code: '<Button><Icon name="check" />送信</Button>' },
  ],
  invalid: [
    // Button の直接の子に Text: 弾く
    {
      code: '<Button><Text textStyle="S1rem_W400_H1.5rem_LS0">送信</Text></Button>',
      errors: [{ messageId: 'noTextInsideButton' }],
    },
    // ネストした Button の中の Text: 弾く
    {
      code: '<Button><span><Text>ラベル</Text></span></Button>',
      errors: [{ messageId: 'noTextInsideButton' }],
    },
  ],
})
```

`valid` に「通るべきコード」、`invalid` に「弾かれるべきコード」と期待するエラーを書く。`RuleTester.run` が通れば実装が仕様に合っていることが保証される。

## CI に組み込む

書いたルールを `eslint.config.js`（flat config）に登録する。数行で済む。

```js
// eslint.config.js
import localPlugin from './plugin/index.js'

export default [
  {
    plugins: {
      local: localPlugin,
    },
    rules: {
      'local/no-text-inside-button': 'error',
    },
  },
]
```

さらに pre-commit フック（husky + lint-staged）を組み合わせると、コミット前に自動で ESLint が走るようになる。

```json
// package.json（抜粋）
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix"]
  }
}
```

これで「AI が生成したコードも、コミット時に ESLint で検証される」環境が整う。規約違反があれば CI が弾き、AI はそのエラーメッセージを読んで自己修正できる。[LLM には厳格な制約を](../../Principles/AIWorkflow/03_LLMには厳格な制約を.md)で書いたように、制約が実行可能であるほど、LLM の自己修正ループは速く閉じる。

## 効果 — 「実行可能な制約」になると何が変わるか

制約が実行可能になると、人間のレビューから「この規約に違反している」という指摘が消える。その指摘は ESLint がやる仕事になるからだ。レビュアーは設計の妥当性や意図の読み取りに集中できる。これは指摘コストの削減ではなく、レビューの質の向上だ。

AI 生成コードについては、さらに大きな変化がある。AI が規約を「読んで守る」のではなく、「実行して弾かれたら直す」ループに入れるようになる。会話が長引いてドキュメントが薄まっても、ESLint は変わらず走る。コンテキストに依存しない制約だからだ。

かつて ESLint カスタムルールの自作は「割に合わない」とよく言われていた。AST の構造を調べ、正しいセレクタを書き、テストを整えるのに時間がかかる割に、そのルールが恩恵をもたらすのは限られたケースだけだった。今は AI がルールのたたき台をすぐに書く。AST の読み方を説明すれば、ほぼ動くコードが出てくる。投資対効果の分岐点が大きく下がった。「割に合わなかった規約の自動化」が割に合うようになった。これも、書き手が変わったことで設計の基準が変わった一例だと思っている。

---

関連する記事として、この規約の出発点である[Button コンポーネントの中で Text コンポーネントを使わない](../StyleGuide/React/10_Buttonの中でTextを使わない.md)、ESLint ルール全般の採用方針をまとめた[ESLint](../StyleGuide/React/16_ESLint.md)、そして「実行可能な制約」という発想の根拠を書いた[ドキュメントより実行可能な制約へ](../../Principles/AIWorkflow/02_実行可能な制約へ.md)と[LLM には厳格な制約を](../../Principles/AIWorkflow/03_LLMには厳格な制約を.md)を参照してほしい。

---

> **執筆時点の補足（2026-06 / ESLint v9）**
> 本文のコード例は ESLint v9 の flat config（`eslint.config.js`）ベースで書いている。v9 では flat config がデフォルトになり、`.eslintrc.*` 形式（legacy config）は非推奨になった。legacy config では `plugins` の登録方法や `parserOptions` の扱いが異なるため、v8 以前の環境ではドキュメントを確認すること。
>
> `RuleTester` の `languageOptions.parserOptions` に `ecmaFeatures: { jsx: true }` を渡しているのは、JSX を標準パーサーで解析するためだ。ESLint v9 の `RuleTester` は flat config 形式の設定（`languageOptions`）を受け取る点に注意（v8 のトップレベル `parserOptions` 形式は使えない）。プロジェクトで `@typescript-eslint/parser` を使っている場合は、`languageOptions.parser` に同パーサーを指定する。
>
> ESLint のカスタムルール API（`meta`、`create`、AST セレクタの仕様）はバージョンをまたいでも安定しているが、マイナー間の変更に備えて公式ドキュメント（eslint.org/docs/latest/extend/custom-rules）を参照されたい。
