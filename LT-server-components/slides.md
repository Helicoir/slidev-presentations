---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
---

# React Server componentsとNextjs

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" alt="GitHub"
    class="text-xl icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

# Server componentsとは

* サーバー側でレンダリングしてクライアントに結果を返す
* 非同期処理をstreamでき、結果をあとから表示可能
* SSRと違い、一部だけサーバー側で、残りはクライアントでレンダリングするなど個別最適化が可能

---

# 従来の困りごと

<div class="cont">
  <img class="h-88 top-margin" src="/images/img_1.png">
</div>

---

# ①データ取得が終わるまで表示できない

<div class="cont">
  <img class="h-88 top-margin" src="/images/img_2.png">
</div>

---

# ②SSRとCSRの結果が違ったらやりなおし

<div class="cont">
  <img class="h-88 top-margin" src="/images/img_3.png">
</div>

---

# SCでどうなるのか

<div class="cont">
  <img class="h-88 top-margin" src="/images/img_4.png">
</div>

---

# うれしいこと

* 従来の困りごと解消
  * データ取得はstreamになる→待たずに初期表示ができる
  * レンダリング結果不一致ケースを抱えたコンポーネントをSSRに含めず処理できる→やりなおし抑制
* 追加でうれしいこと
  * サーバーから返す部分はキャッシュすることが可能→パフォーマンス向上

---

# 新しいreactを牽引するNextjs
<div class="cont">
  <img class="h-88 top-margin" src="/images/img_5.avif">
</div>

---

<style>
.footnotes-sep {
  @apply mt-20 opacity-10;
}
.footnotes {
  @apply text-sm opacity-75;
}
.footnote-backref {
  display: none;
}
.cont {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  height: 300px;
  margin: 40px 0 0;
}
</style>

