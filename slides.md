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

# Intro

今日話すこと・話さないこと

- アジェンダ
  - 2023年のNext.js動向
  - App Router is 何？
  - App Router10分チュートリアル
  - App Routerにすべきか
- 話さないこと
  - Next.jsやReactの基礎
  - experimentalな機能群

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

結論: そんなことはない

- 機能開発は停滞気味だが一応サポート宣言はされてる
- Pages Routerはいわゆる「枯れてる」状態
  - 利用できる膨大なエコシステムで、メリットは大きい
  - App Routerやめた話もちらほら...
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

- Client Component: 従来からあるクライアント・サーバーどちらでもレンダリング可能なComponent
- Server Component: 新たに**サーバー側でのみレンダリングされる**Component
- 概念自体はNext.js固有のものではないので、今後サポートするフレームワークは増えていくと予想
  - Remix
  - viteベースのVike

---

# Client Components

従来からあるComponent

- ファイルの先頭に`"use client"`があるとそのComponent以下は全てClient Componentとなる
- Next.jsにおいては**SSR時にもレンダリングされる**
- props以外で**Server Componentsを含むことはできない**

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

# Server Components(RSC)

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

```tsx
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

```tsx
// app/page.tsx
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
    </>
  );
}
```

---

# Client Components + `useState`

`useState`を使うために`"use client"`を追加

```tsx {all|2-4,7|18,31}{maxHeight:'320px'}
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

# Server Actions

todo: サンプル実装の作成

---
layout: section
---

# App Routerにすべきか

---

# App Routerの現状

ポイントとしては以下3つ。

- App Routerはstable
- Pages Routerから大きなパラダイムシフトが必要
- Vercel以外で利用するには深い理解が必要となる

---

# App Routerはstable

- 破壊的変更を極力行わないという意味でのstable
- **stable=プロダクションReadyではない**
- バグ多かったが、最近は少しずつ安定に向かってる（？）

---

# Pages Routerから大きなパラダイムシフトが必要

- シンプルになる部分もあれば複雑になる部分もある
- これまでの知識を流用できる部分もあれば学習が必要な部分もある

---

# Vercel以外で利用するには深い理解が必要となる

- 特にCache周り
- Cacheの多くをOpt outしちゃうのも手

---
layout: quote
---

# End
