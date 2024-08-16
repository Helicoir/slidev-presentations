---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://picsum.photos/1920/1080
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

# なぜ次のエアクロフロント基盤ではSSRをやめるのか

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

# 概要

* 次のエアクロフロント基盤の設計を見てみよう
* なぜSSRをやめるのか
* Next.jsを使わない理由

---

# whimsicalをみる
* https://whimsical.com/DwusnG2ZyXPDskADehVwfB

---

# なぜSSRをやめるのか

<ul>
 <li style="font-weight: bold; color: white;">SEOを考慮する必要がない領域だから</li>
 <li style="font-weight: bold; color: white;">静的リソースのほうがデプロイ・運用しやすいから</li>
 <li style="font-weight: bold; color: white;">Reactの最新機能のうちSSRが絡む領域は、難しすぎてついていけないから</li>
</ul>

---

# SEOを考慮する必要がない領域だから

whimsical参照

---

# 静的リソースのほうがデプロイ・運用しやすいから

* インスタンス立てて、pm2でプロセス管理して、サーバー側でいろいろ処理して、etc
  * やること多すぎる
* s3 + cloudfrontにすることで、
  * s3にuploadするだけでデプロイが完了するので、楽
  * レンダリング処理などを挟むサーバーが存在しないので、高速
  * インスタンスが存在しないのでそもそも安く、スケーリングなども考えなくてよい
  * cloudfrontで複数リソースをまとめて管理 & リクエストに対する追加処理を挟めるので、処理が分散しない
  * cloudfrontをうまく扱えば、今までより簡単に検証環境・blue/green環境を増やしたりできるので、運用の柔軟性が上がる

---

# SSRしたほうが嬉しいこと

* 事前にサーバーでさまざまな処理を挟んでユーザーに届けられるため、柔軟性の高いアプリケーションが構築できる
  * アプリケーション層でやるよりもっと早い層でやったほうがより高速
  * 現状エアクロでもreact ssrサーバーでリダイレクトなどを書くことよりも、nginx層でそのへんを済ませていることが多く、これがcloudfrontに移るイメージ
  * さらに複雑な処理やjsが絡む処理についてもcloudfront function, lambda@edge などで代用可能になってきている
  * もちろんreactのSSR自体の価値は落ちてない

---

# SSRしたほうが嬉しいこと

* ReactのSSRのほうが理論値は高い
  * 最新のNext.jsのベスプラにのっとってやれれば、API requestのキメ細かなキャッシュ戦略を作れたり、
  * SSRで作ったHTMLを効率よく扱えればレンダー速度も改善される
  * 柔軟性が高く、かつ高速になる
  * ↑無理です
    * 難しすぎる
    * Next.js独自のやり方が多すぎる
    * ベンダーロックインな機能も多く、Vercel上に構築せざるを得なくなりそう
    * SSRサーバーがシンプルに負荷高くて高い

---

# Reactの最新機能のうちSSRが絡む領域は、難しすぎてついていけないから

* React v18
  * Suspense
  * Server Component
* Next.jsの新機能
  * App router & custom fetch
  * server actions
* これらに対応するために必要なこと
  * SSR, CSRの違い
  * サーバーとクライアントそれぞれに書かないといけない処理の目利き
  * エンドポイント単位のキャッシュに対する理解

→ReactでSSRするときに気をつけないといけないことをすべて守ったうえでこれやらないとパフォーマンス落ちる
ついていけないです

---

# Next.jsを使わない理由

* Next.jsはサーバーサイド最適化フレームワークになりつつある
  * SSR, CSR, SSG全部含めた最適化をサーバーで行うためのフレームワーク
  * サーバーなしのall CSRなアプリケーションを作るためのフレームワークではなくなってきている
* Next.jsはWebpack依存から離れられない
  * viteと比べるとどうしても重い
  * 互換性のある後継ライブラリのTurbopackも出してるけど、後述のベンダーロックインを勧めてくる感じがイヤ
* Next.jsの方針自体に不安
  * Vercelに乗せないと性能を発揮できない機能が存在するのでベンダーロックインされる
  * ReactのExperimentな機能をNext.jsでは正式版に内包している
  * どんどん勝手に進化してくし勝手に色々変えすぎ

---

# Remixを使う理由

* Viteベースで速い
* SSRフレームワークだけど、Web標準の機能になるべく沿う方針で作られている
* SPAモードを公式に提供しているため長期のサポートが期待できる

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

