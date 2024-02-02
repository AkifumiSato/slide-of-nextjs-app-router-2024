---
theme: default
# https://unsplash.com/ja/%E5%86%99%E7%9C%9F/%E7%B7%91%E8%B5%A4%E9%9D%92%E3%81%AE%E5%85%89--MzXKfizmQs
background: /assets/top.jpg
class: text-center
highlighter: shikiji
lineNumbers: false
drawings:
  persist: false
transition: slide-left
title: App Router入門
mdc: true
---

# App Router入門

Next.js App Router@2024.01

---

# Agenda

今日話すこと・話さないこと

- 話すこと
  - 2023年のNext.js動向振り返り
  - App Router is 何？
  - App Router Short Tutorial
  - App Routerにすべきか
- 話さないこと
  - Next.jsやReactの基礎
  - experimentalな機能群
  - App Routerを実案件で使う際の注意点

---
layout: section
---

# 2023年のNext.js動向

---

# 2023年のNext.js動向概要

各機能や用語の詳細は後述、Next.jsの動向概要

- App Router(`app`)の機能開発多数
- App Routerのstable宣言
- Server Actionsの実装
- Pages Router(`pages`)のメンテナンスは停滞気味

---

# Pages Routerはもう使わないべき？

結論: そんなことはない。まだまだ現役

- 機能開発は停滞気味だが一応サポート宣言はされてる
- Pages Routerはいわゆる「枯れてる」状態
  - 利用できる膨大なエコシステムで、メリットは大きい
  - 実際App Router採用後乗り換えた話などもちらほら...
- ただし、将来的にApp Routerへの移行が必須になる可能性もある
  - https://nextjs.org/blog/next-13-4#is-the-pages-router-going-away
  - 現時点ではどうなるか予想できない

---
layout: section
---

# App Router is ...

---

# App Router is ...

App RouterはNext.jsにおける新たなルーティングと新機能

- Next.jsの新しいRouter
  - `pages`ではなく`app`ディレクトリ配下に配置する
  - **フレームワークとしてはほとんど別物レベル**
- Reactの新機能が使える
  - Client Components/Server Components
  - Server Action
- 強力なCache
- その他諸々新機能（多くて混乱するので割愛）

---

# Client Components/Server Components

Next.jsではなくReactにおける新たな概念

- Client Component: 従来からあるクライアント・サーバー**どちらでもレンダリング可能**なComponent
- Server Component: 新たに**サーバー側でのみレンダリングされる**Component。略してRSCとも呼ばれる
- 概念自体はNext.js固有のものではないので、今後サポートするフレームワークは増えていくと予想
  - Remix
  - viteベースのVike

---

# Client Components

従来からあるComponent

- ファイルの先頭に`"use client"`があるとそのComponent以下は全てClient Componentとなる
- Next.jsにおいては**SSR時にもレンダリングされる**
- propsを除き、Server Componentsを含むことはできない

```tsx {all|1|3,7}
"use client"

import { useState } from "react";
import { Child } from "@/components/Child";

export function Counter() {
  const [count, setCount] = useState(0) // Client Componentのみで利用可能

  return (
    <div>
      <p>count: >{count}</p>
      <button onClick={() => setCount(prev => prev + 1)}>increment</button>
      <Child />
    </div>
  )
}
```

---

# Server Components

サーバーでのみ実行されるComponent

- App RouterではデフォルトでServer Componentsとなる
- Component自体をasyncにすることが可能で、fetchを直接扱える
- Client Components/Server Componentsどちらも含めることができる

```tsx {all|4|1,11}
import { Counter } from "@/components/Counter"; // Client Component

export default async function Page() {
  const product = await fetch('https://dummyjson.com/products/1').then(res => res.json())

  return (
    <div>
      <pre>
        <code>product: {JSON.stringify(product)}</code>
      </pre>
      <Counter />
    </div>
  )
}
```

---

# Server Action

formの`action`からサーバー側の関数を実行できる

- `"use server"`と書くことでサーバーサイドで実行される関数となる

```tsx {all|3-9|11}
// Server Component
export default function Page() {
  async function create(formData: FormData) {
    "use server"

    const text = formData.get('text')
    // ...APIへPOSTしたりDBに保存するなど...
  }
 
  return (
    <form action={create}>
      <input type="text" />
      <button type="submit">Create</button>
    </form>
  )
}
```

---

# 強力なCache

4層のCache層があり、多くがデフォルトで有効なためパフォーマンスが非常に良い

<div class="flex justify-center">
  <img src="/assets/next-cache.png" class="w-100">
</div>

---
layout: section
---

# Short Tutorial

---

# Hello world.

https://github.com/vercel/next.js/tree/canary/examples/hello-world

```shell
$ pnpm create next-app --use-pnpm --example hello-world hello-world-app
```

---

# Nested layout

`layout.tsx`を修正して`lang="ja"`を設定

```tsx {all|8}
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
   <html lang="ja">
      <body>{children}</body>
    </html>
  );
}
```

---

# Server Components + fetch

dummyデータをfetchして表示

```tsx {all|3-5}
// app/page.tsx
export default async function Page() {
  const product = await fetch('https://dummyjson.com/products/1')
    .then((res) => res.json())
    .finally(() => console.log('>>> fetch dummyjson'));

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <pre>
        <code>{JSON.stringify(product, null, 2)}</code>
      </pre>
    </>
  );
}
```

---
transition: fade
---

# Client Components + `useState`

`useState`を使うために`"use client"`を追加

```tsx {all|2-4,7}
// components/Counter.tsx
"use client";

import { useState } from "react";

export function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>count: {count}</p>
      <button onClick={() => setCount(count + 1)}>increment</button>
    </div>
  );
}
```

---

# Client Components + `useState`

`useState`を使うために`"use client"`を追加

```tsx {all|2,15}
// app/page.tsx
import { Counter } from "../components/counter";

export default async function Page() {
  const res = await fetch('https://dummyjson.com/products/1')
    .then((res) => res.json())
    .finally(() => console.log('>>> fetch dummyjson'));

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <pre>
        <code>{JSON.stringify(res, null, 2)}</code>
      </pre>
      <Counter />
    </>
  );
}
```

---
transition: fade
---

# Server Actions

formからサーバー側の関数を実行

```ts
// app/action.ts
"use server";

export async function action(data: FormData) {
  // skip validation
  const name = data.get("name");
  console.log(`action called with name: "${name}"`);
  // ...APIへPOSTしたりDBに保存するなど... 
}
```
---

# Server Actions

formからサーバー側の関数を実行

```tsx {all|3,15}
// app/page.tsx
import { Counter } from "../components/counter";
import { action } from "./action";

export default async function Page() {
  // ...

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <pre>
        <code>{JSON.stringify(res, null, 2)}</code>
      </pre>
      <Counter />
      <form action={action}>
        name: <input type="text" name="name"/>
        <button>submit</button>
      </form>
    </>
  );
}
```

---
layout: section
---

# App Routerにすべきか

---

# App Routerの現状

App Routerはエコシステム含め未成熟な段階

- Next.jsとしてはApp Routerはstableと宣言してるが、実際には未成熟な状態
  - まだまだApp Routerにはバグが多い傾向
  - 関連ライブラリが未成熟
- 学習コスト・対応コスト高
  - Pages Routerから大きなパラダイムシフトが必要
  - Vercel以外で利用するには深い理解が必要となる
- 実験的採用なら良いが採用は慎重に

---

# App Router採用時の注意点

実験的でも良いから採用してみたい、という場合の注意点

- Vercel以外での利用は難易度が高い
  - Cacheのハンドリングなどを自前で行う必要あり
- VercelでもCache周りでのはまりポイントが多い
- `next dev`と`next build && next start`で挙動が異なるケースがある
- static export周りでもバグが多い（らしい）

---
layout: quote
---

# App RouterはNext.jsの将来を担う存在

今日時点での採用は慎重に

---
layout: center
---

# End
