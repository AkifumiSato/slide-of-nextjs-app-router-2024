---
layout: section
---

# App Router

---

# App Routerとは

https://nextjs.org/docs/app

> The Next.js App Router is a new paradigm for building applications using React's latest features.
> 
> App Routerは、Reactの最新機能を使用してアプリケーションを構築するための新しいパラダイムです。

- Next.jsの新しいRouter
  - 従来のRouterはPages Routerと呼称
  - フレームワークとしてはほとんど別物レベル
- Client Components/Server Components
- 強力なCache
- Server Action

---

## Server Components

従来からあるReact ComponentをClient Componentと呼称し、新たに**サーバー側でのみレンダリングされる**ComponentとしてReact Server Componentが追加された。

- データフェッチがComponent内でより直感的に描ける
- サーバーでのみ実行されるのでAPI keyの漏洩考慮などが不要
- バンドルサイズが減る
- etc...

```tsx {all|3}
// example of Server Component
export default async function Page() {
  const product = await fetch('https://dummyjson.com/products/1').then(res => res.json())

  return (
    <div>
      product: {JSON.stringify(product)}
    </div>
  )
}
```

---

## 強力なCache

4層のCache層があり、多くがデフォルトで有効なためパフォーマンスが非常に良い

<div class="flex justify-center">
  <img src="/assets/next-cache.png" class="w-100">
</div>

---

## Server Action

Client Componentのformからサーバー側の関数を実行できる。

```tsx {all|3-9}
// Server Component
export default function Page() {
  // Server Action
  async function create(formData: FormData) {
    'use server'
    
    const text = formData.get('text')
    // ...
  }
 
  return (
    <>
      <form onSubmit={ create }>
        <input type="text" />
        <button type="submit">Create</button>
      </form>
    </>
  )
}
```

---
layout: section
---

# App Routerの現状

---

# App Routerの現状

ポイントとしては以下3つ。

- App Routerはstable
- Pages Routerから大きなパラダイムシフトが必要
- Vercel以外で利用するには深い理解が必要となる

---

## App Routerはstable

- 破壊的変更を極力行わないという意味でのstable
- **stable=プロダクションReadyではない**
- バグ多かったが、最近は少しずつ安定に向かってる（？）

---

## Pages Routerから大きなパラダイムシフトが必要

- シンプルになる部分もあれば複雑になる部分もある
- これまでの知識を流用できる部分もあれば学習が必要な部分もある

---

## Vercel以外で利用するには深い理解が必要となる

- 特にCache周り
- Cacheの多くをOpt outしちゃうのも手
