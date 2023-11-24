---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: "text-center"
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

# PDJ フロント アーキテクチャ紹介

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

# 目次

- 採用技術
- 全体構成
- その他備考

---

# 採用技術

- React v18
- SSR + SSG
- React Query
- React Redux + Redux-toolkit
- その他

---

# React v18

- 並行レンダリング（=レンダリング状態の分離）
  - Suspense 機能
  - startTransition

ざっくり言うと、

コンポーネントの内部において、
レンダリングや処理をトリガー後、結果が確定するまで別の view を返す
& エラーを独立して処理できる

---

# SSR + SSG

```md {all|2-4|5-8|9|10}
- ページごとに動的 or 静的に生成するかを決められる
  - 動的: Server-side Rendering
    - リクエストが来たら、サーバーサイドで処理を行ったあと、view が返される
    - サーバーサイドであらかじめ動的データを入れてくれるので、api から返されるデータをファーストビューや head タグに含めたいとき便利
  - 静的: Server-side Generating
    - ビルドするときに、サーバーサイドで処理を行ったあと、html, json を生成して置いとく
    - リクエストが来たらその html, json を返す
    - 処理が挟まらないので高速
      - ファーストビューに含まれるデータはビルド時に確定してしまうので、最新情報をファーストビューや head タグに含めたいときは使えない
    - Vercel にデプロイすると n 秒ごとに再生成する設定ができる（Incremental Static Regeneration）
```

---

# SSR + SSG

```ts {all|11-19}
const CoordinatesPage = (props: Props) => {
  MapDehydratedPropsHelper({
    dehydratedState: props.dehydratedState,
  });

  return (
    // ~~~~~~
  );
};

export async function getStaticProps() {
  const queryClientForPrefetch = new QueryClient();
  await useCoordinatesPrefetcher({ queryClientForPrefetch });
  return {
    props: {
      dehydratedState: dehydrate(queryClientForPrefetch),
    },
  };
}

export default CoordinatesPage;
```

---

# おまけ hydration

```ts {all|4|10-22|13|17-20}
export const MapDehydratedPropsHelper = function ({
  dehydratedState,
}: MapDehydratedPropsHelperType) {
  const formattedQueries = formatDehydratedQueries(dehydratedState.queries);
  distributeStateByQueryKey(formattedQueries);

  return formattedQueries;
};

const formatDehydratedQueries = (
  dehydratedQueries: DehydratedState["queries"]
) => {
  const apiEndpointEntries = Object.entries(APIEndpoints);
  return dehydratedQueries.map((query) => {
    const queryKeyUrl = query.queryKey[0];

    return {
      key: apiEndpointEntries.find((entry) => entry[1] === queryKeyUrl)?.[0],
      ...query.state,
    };
  });
};
```

---

# おまけ hydration

```ts {5|10-16|13-15}
export const MapDehydratedPropsHelper = function ({
  dehydratedState,
}: MapDehydratedPropsHelperType) {
  const formattedQueries = formatDehydratedQueries(dehydratedState.queries);
  distributeStateByQueryKey(formattedQueries);

  return formattedQueries;
};

const distributeStateByQueryKey = (
  dehydratedQuery: FormattedDehydratedQuery
) => {
  dehydratedQuery.forEach((q) => {
    console.log(q.key, q); // ここでreduxに突っ込んだりする
  });
};
```

---

# React Query

```md {all|2-6}
- データフェッチ効率化 & ハンドリング向上用
  - 文字列キーとリクエストがセットで管理される
  - 同一キーのリクエストはブラウザキャッシュが即座に帰ってくる
    - 取得自体は裏側で走っており、新しいデータの取り扱いも簡単にできる
  - Suspense 対応
    - データフェッチのエラー影響を一部のコンポーネントだけに留めることが可能（画面がクラッシュしない）
```

---

# React Query

```ts {all|8-22}
export const PostSamples = (qc: QueryClient) => {
  const { post } = useSamplesMutation();
  // const { put } = useXXXXXMutation();

  const toast = useToast();

  const execute = (payload?: any) => {
    post(payload, {
      onSuccess: () => {
        // トーストを出して成功したことをユーザーに認知させる
        toast({
          title: "Completed!",
          description: "サンプルの作成に成功しました。",
          status: "success",
          duration: 5000,
          isClosable: true,
        });

        // 該当キーのクエリキャッシュを潰す。
        // このとき有効な画面で使われている（＝アクティブな）クエリのキャッシュを潰すと、即座に再フェッチが実行される
        qc.resetQueries([APIEndpoints.SAMPLES]);
      },

      onError: (e) => {
        // 失敗したことをユーザーに通知
        toast({
          // ~~~
        });

        // カスタムエラーオブジェクトの送信
        // newRelicのエラー送信イベントを叩いてもヨシ
        throw new Error("asdfadfasd");
      },
    });

    // put(payload, {
    // ~~~
    // });
  };

  return { execute };
};
```

---

# React Query

```ts {8-18|all}
export const PostSamples = (qc: QueryClient) => {
  const { post } = useSamplesMutation();
  // const { put } = useXXXXXMutation();

  const toast = useToast();

  const execute = (payload?: any) => {
    post(payload, {
      onError: (e) => {
        // 失敗したことをユーザーに通知
        toast({
          // ~~~
        });

        // カスタムエラーオブジェクトの送信
        // newRelicのエラー送信イベントを叩いてもヨシ
        throw new Error("asdfadfasd");
      },
    });

    // put(payload, {
    // ~~~
    // });
  };

  return { execute };
};
```

---

# React Redux + Redux-Toolkit

```md {all}
- 基本の考え方は変わらない
  - state, reducer, actions
  - reducer 直接叩かない（actions 経由でデータ変更する）
- +α で selector という概念も登場
  - state + state の値を加工したり特定ロジックを通した結果を返す値のオブジェクト
  - DDD の Entity 的な立ち位置（状態変更ロジックは持てないけど）
  - 各コンポーネントで計算していた state の加工ロジックをここに乗せて分離する
    - エアクロの診断導線で途中回答によって質問が変わったりするロジックを redux の内部に閉じ込めるなど
- ほかいろいろできる（調査中）
```

---

# その他

```md {all}
- React-hook-form
  - いい感じの form
- Zod
  - value object 兼バリデーションの提供
- Chakra UI
  - なるべく生の CSS は書かない方向へ
  - すべてコンポーネントの props で style つけてく（型付いてるよ！うれしい！）
- Sass （Scss）
- Storybook
- testing
  - jest
    - い つ も の
  - testing-library
    - 描画結果に対するテスト
```

---

# 全体構成（の大事なところ）

```md {all|2-3|4-10|11-16|17-18|19}
src/
├ pages/
│ └ (pagePath).ts
├ components/
│ ├ layouts
│ ├ templates
│ ├ organisms
│ ├ molecules
│ ├ atoms
│ └ helpers
├ hooks/
│ ├ fetchers/
│ ├ mutations/
│ ├ services/
│ │ └ (domain).ts/
│ └ helpers/
├ redux/
│ └ (domain).ts/
└ usecases/
```

他: configs, assets, utlils...

---

# どうしたらフロントのテストって書けるんだろう

<div class="cont">
  <p class="center">①業務ロジックをコンポーネントに書かない</p>
  <p class="center">②テストしやすい粒度にロジックを分解する</p>
  <p class="center">③↑がしやすいようなライブラリや構成でつくる</p>
</div>

---

# どうしたらフロントのテストって書けるんだろう

<div class="cont">
  <img class="h-88 top-margin" src="/images/img.png">
</div>

---

# どうしたらフロントのテストって書けるんだろう

<div class="cont">
  <img class="h-88 top-margin" src="/images/img2.png">
</div>

---

# どうしたらフロントのテストって書けるんだろう

<div class="cont">
  <p class="center">おわり！</p>
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
.center {
  text-align: center;
}
.top-margin {
  padding: 40px 0 0;
}
</style>
