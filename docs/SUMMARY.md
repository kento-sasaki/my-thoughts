# Summary

- [🤔 My Thoughts](./README.md)

## 原則・価値観

- [⚙️ 設計の原則](./Principles/Engineering/README.md)
    - [最小依存性：親に依存しないコンポーネント](./Principles/Engineering/01_最小依存性.md)
    - [命名は言語](./Principles/Engineering/02_命名は言語.md)

## 実践・技術

- [📐 コード規約](./Practices/StyleGuide/README.md)
    - [React の規約](./Practices/StyleGuide/React/README.md)
        - [親に依存するコンポーネントを作らない](./Practices/StyleGuide/React/00_親に依存しない.md)
        - [React.Fragment で全体をラップしたコンポーネントを作らない](./Practices/StyleGuide/React/01_Fragmentで全体をラップしない.md)
        - [props や state に依存しない関数・変数はコンポーネントの外に書く](./Practices/StyleGuide/React/02_props_stateに依存しない関数は外に出す.md)
        - [React.JSX.Element を返す関数を定義しない](./Practices/StyleGuide/React/03_JSXを返す関数はコンポーネントにする.md)
    - [TypeScript の規約](./Practices/StyleGuide/TypeScript/README.md)
        - [Enum を switch 文で網羅する](./Practices/StyleGuide/TypeScript/05_Enumはswitchで網羅する.md)
