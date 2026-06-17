# React の規約

第1部の[設計の原則](../../../Principles/Engineering/README.md)を、React の現場ルールに落としたもの。各規約は対応する原則へ相互リンクしている。

## 規約一覧

- [親に依存するコンポーネントを作らない](./00_親に依存しない.md)
- [React.Fragment で全体をラップしたコンポーネントを作らない](./01_Fragmentで全体をラップしない.md)
- [props や state に依存しない関数・変数はコンポーネントの外に書く](./02_props_stateに依存しない関数は外に出す.md)
- [React.JSX.Element を返す関数を定義しない](./03_JSXを返す関数はコンポーネントにする.md)
- [フロントエンドの命名規則](./04_命名規則.md)
- [コンポーネントの抽象度が上がったら命名を見直す](./05_抽象度が上がったら命名を見直す.md)
- [条件付き hooks は Container パターンで解決する](./06_条件付きhooksはContainerで解決する.md)
- [不要な export をしない](./07_不要なexportをしない.md)
- [useEffect は関連ロジックをまとめる](./08_useEffectは関連ロジックをまとめる.md)
- [useState で前の値を正しく更新する](./09_useStateは前の値で更新する.md)
- [Button コンポーネントの中で Text コンポーネントを使わない](./10_Buttonの中でTextを使わない.md)
- [コンポーネントに margin を持たせない](./11_コンポーネントにmarginを持たせない.md)
- [フロントエンドのディレクトリ構成](./12_ディレクトリ構成.md)
- [Context 導入時のモック共通化](./13_Context導入時のモック共通化.md)
- [mockGraphQLQuery と useMutation にジェネリクスを指定する](./14_mockとuseMutationにジェネリクスを指定する.md)
- [useMutation の onCompleted と onError を活用する](./15_useMutationのonCompletedとonErrorを使う.md)
- [ESLint](./16_ESLint.md)
