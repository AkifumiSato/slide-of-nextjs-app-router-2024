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
- Server Components/Client Components
- 強力なCache
- Server Action

---

# feature: Server Components/Client Components

TBW

---

# feature: 強力なCache

TBW

---

# feature: Server Action

TBW

---

# App Routerの現状

- App Routerはstable
  - 破壊的変更を極力行わないという意味でのstable
  - stable=プロダクションReadyではない
  - 実際v14.0.0時点ではバグが多くとても採用できる状態になかった
  - 少しずつ安定に向かってる（？）
- Pages Routerから大きなパラダイムシフトが必要
  - シンプルになる部分もあれば複雑になる部分もある
  - これまでの知識を流用できる部分もあれば学習が必要な部分もある
- Vercel以外で利用するには深い理解が必要となる
  - 特にCache周り
