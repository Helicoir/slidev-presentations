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

# frontend cache

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

# フロントエンドで使われるキャッシュの話

* インメモリキャッシュ
* Service worker キャッシュ
* ブラウザキャッシュ

https://zenn.dev/kaa_a_zu/articles/f1430cf681b185

---

# インメモリキャッシュ（Rendererプロセス）

* ブラウザのRendererプロセスにおけるメモリのキャッシュ
* Random Access Memory（RAM）を使ったキャッシュ
* 正直よく分かってない

https://zenn.dev/sprout2000/books/6f6a0bf2fd301c/viewer/13319

---

# Service Workerキャッシュ

* httpをinterceptしてキャッシュ機構を仕込める
* 代表的な使われ方
  * オフライン対応（オフラインキャッシュ）
    * スプシのオフライン編集とかもおそらくこれ
  * ファイルの事前キャッシュ（プリキャッシュ）
  * PWA（上記ほか様々な技術の合わせ技で実現される）


---

# ブラウザキャッシュ

* 二種類ある
  * memory cache
  * disk cache

---

# memory cache

* ブラウザのRAMを使ったキャッシュ
* アクセス時受け取ったリソースの一時的なリソース保管
* 一度表示した画像をすぐ表示したりするのに役立つ
* タブ閉じると消える

---

# disk cache

* 「ブラウザキャッシュ」と言われるものの殆どはこれを指している
* ハードディスクやSSDなどの永続的なストレージデバイスに存在するキャッシュ
* 容量が大きく、長期の保存に適する
* フロントエンドで最近よく使われるレスポンスキャッシュ機構はこれを使っていることが多い

「キャッシュを削除する」とは:  https://liginc.co.jp/403436


controllable cache: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control


共有キャッシュとプライベートキャッシュ: https://shukapin.com/infographicIT/cache

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
</style>

