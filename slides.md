---
theme: default
# https://unsplash.com/ja/%E5%86%99%E7%9C%9F/%E7%B7%91%E8%B5%A4%E9%9D%92%E3%81%AE%E5%85%89--MzXKfizmQs
background: /assets/top.jpg
class: text-center
highlighter: shikiji
lineNumbers: false
info: 2024年におけるNext.jsの動向
drawings:
  persist: false
transition: slide-left
title: Welcome to Slidev
mdc: true
---

# Next.js 2024

---
layout: section
---

# Next.jsの動向

---

# Next.jsの動向

- Pages Router（Next.j@v13まで主だった`pages`配下のルーティング）のメンテナンスは停滞気味
- App Routerの開発は進んでいる

---

## Pages Routerはもう使わないべき？

- Pages Routerはいわゆる「枯れてる」状態
- 利用できる膨大なエコシステムで、メリットは大きい
- ただし、将来的にApp Routerへの移行が必須になる可能性もある
  - https://nextjs.org/blog/next-13-4#is-the-pages-router-going-away

---
src: ./pages/app-router.md
---

---

# 構成メモ

- Next.jsの現状
  - Pages routerのメンテナンスは停滞気味
  - App Routerの開発は進んでいる
- done: App Routerについて
- ではApp Routerを選ぶべきなのか？
- 混沌の2024
  - App Routerとか出てきて辛いという声もある
  - じゃあ他に行くべきか？
- まとめ
