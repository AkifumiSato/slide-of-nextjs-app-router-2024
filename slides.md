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

# Profile

- name: 佐藤 昭文（Akifumi Sato）
  - twitter: akfm_sato
  - github: AkifumiSato
  - zenn.dev: akfm
  - Web Engineer
- Next.js
  - 仕事でもNext.js（Pages Router）のプロジェクトを担当
  - 自身のサイトなどもNext.js（App Router）
  - App Routerに強い興味あり

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
  - Client Components/**Server Components**
  - Server Actions
- 強力なCache
- その他諸々新機能（多くて混乱するので割愛）

---

# 新機能にみるReact×Serverの進化

Reactがクライアント中心であるが故に起きていた、様々な問題を解決したかった

- デフォルトでより良いパフォーマンスの達成
  - ハイドレーション処理・バンドルサイズを減らせる
  - クライアント<->サーバーの通信を減らせる
    - （サーバー<->サーバー間の通信の方が早いことが多い＋よりセキュア）
- バックエンドへのフルアクセス
  - より簡単に、Componentが必要なデータを取得できるようになった
  - 中間層(tRPC, GraphQL, API Routesなど)の実装や設計が不要に

[//]: # (参考: https://github.com/reactjs/rfcs/blob/main/text/0188-server-components.md#motivation)

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
- `useState`などのhooksは**利用できない**
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

`layout.tsx`を修正して共通のヘッダーを作成

```tsx {all|10}
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="ja">
      <body>
        <header>all page's header</header>
        {children}
      </body>
    </html>
  );
}

```

---
transition: fade
---

# New page

ディレクトリを作成して新しいページを作成

```tsx
// app/products/page.tsx
export default function Page() {
  return <h1>Hello, Products page!</h1>;
}
```

---

# New page

ディレクトリを作成して新しいページを作成

```tsx {all|2,8}
// app/page.tsx
import Link from "next/link";

export default function Page() {
  return (
    <>
      <h1>Hello, Next.js!</h1>
      <Link href="/products">/products</Link>
    </>
  );
}

```

---

# Server Components + fetch

dummyデータをfetchして表示

```tsx {all|2|3-5}
// app/page.tsx
export default async function Page() {
  const product = await fetch('https://dummyjson.com/products/1')
    .then((res) => res.json())
    .finally(() => console.log('>>> fetch dummyjson'));

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <Link href="/products">/products</Link>
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
// components/counter.tsx
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

```tsx {all|2,17}
// app/page.tsx
import { Counter } from "../components/counter";
import Link from "next/link";

export default async function Page() {
  const res = await fetch('https://dummyjson.com/products/1')
    .then((res) => res.json())
    .finally(() => console.log('>>> fetch dummyjson'));

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <Link href="/products">/products</Link>
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

```tsx {all|3,17}
// app/page.tsx
import { Counter } from "../components/counter";
import { action } from "./action";
import Link from "next/link";

export default async function Page() {
  // ...

  return (
    <>
      <h1>Hello, Next.js!</h1>
      <Link href="/products">/products</Link>
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
transition: fade
---

# Dynamic route

従来同様、`[id]`のようなページも作成可能

```tsx
// app/products/[id]/page.tsx
export default function Page({ params }: {
  params: { id: string }
}) {
  return <h1>Hello, Products {params.id} page!</h1>;
}
```

---

# Dynamic route

従来同様、`[id]`のようなページも作成可能

```tsx
// app/products/page.tsx
import Link from "next/link";

export default function Page() {
  return (
    <>
      <h1>Hello, Products page!</h1>
      <Link href="/products/111">/products/111</Link>
    </>
  );
}
```

---

# revalidate

defaultで強力にキャッシュされる

```tsx {all|14,15}
// app/products/page.tsx
import Link from "next/link";

export default function Page() {
  return (
    <>
      <h1>Hello, Products page!</h1>
      <Link href="/products/111">/products/111</Link>
      <p>random: {Math.random()}</p>
    </>
  );
}

// next build && next startでのみキャッシュが3.0s有効
export const revalidate = 3;
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
- フレームワーク自体のはまりポイントが多い
  - Cache周り
  - `next dev`と`next build && next start`で挙動が異なるケースがある
  - static export周りでもバグが多い（らしい）
- storybookやテスト周りなどが未成熟

---
layout: quote
---

# App RouterはNext.jsの将来を担う存在

今日時点での採用は慎重に

---
layout: center
---

# End
