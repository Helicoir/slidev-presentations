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

# github cliで遊ぼう

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

# github cli

* github上のPR, issue, commitなど様々なアセットを管理・検索・修正できる
* workflowの実行なども可能
  * https://cli.github.com/
* 例
  * 案件名をブランチ名/PRの本文に含んだPRの一覧を取得する（gh pr search）
  * PRをコマンドから対話的に生成する（gh pr create）
  * PRにasignee, label, milestoneなどを付与する（gh pr edit）
  * github workflowをコマンドラインからrunする&終了時に追加で任意のコマンドを実行する（gh run & [command]）

---

# 例


```bash {all}
#!/bin/bash
gh search issues --repo Helicoir/nuxt3-playground --label enhancement

# Helicoir/nuxt3-playground	9	open	機能Bの実装	enhancement	2023-03-10T01:43:13Z
# Helicoir/nuxt3-playground	8	open	機能Aの実装	enhancement	2023-03-10T01:42:15Z
```

---

# 例

```bash {all}
#!/bin/bash
gh pr create --base master --title "test PR" --body "いろいろ" --label enhancement

gh pr edit "2023/03/10" --repo Helicoir/nuxt3-playground --add-label documentation

gh pr edit "2023/03/10" --repo Helicoir/nuxt3-playground --remove-label documentation
```

---

# 例

```bash {all}
#!/bin/bash
set -euC
repo_list=(`gh repo list air-closet -L 1000 | awk -F " " '{print $1}'`)

# air-closet/GAS
# air-closet/air-closet-infrastructure
# air-closet/air-closet-console
# ...

for repo in "${repo_list[@]}"
do
  gh pr list --search "AIRCLOSET-37244 in:title,body state:closed" -L 100 -R ${repo}
done

# Showing 2 of 2 pull requests in air-closet/ac-web-static that match your search
# #214  feature/AIRCLOSET-37244-42498-disableABtest: 診断結果...   feature/AIRCLOSET-37244-42498-disableABtest  about 16 days ago
# #212  feature/AIRCLOSET-37244-42498: 診断結果画面アップデー...   feature/AIRCLOSET-37244-42498                about 20 days ago
# no pull requests match your search in air-closet/GAS
# ...
# Showing 8 of 8 pull requests in air-closet/air-closet-ssr that match your search
# #2571  AIRCLOSET-37244 fix null fvInfo error                 feature/AIRCLOSET-37244-b                        about 20 days ago
# #2570  AIRCLOSET-37244 診断結果画面アップデート              feature/AIRCLOSET-42497+42499                    about 20 days ago
# #2563  AIRCLOSET-37244 診断結果画面アップデート              feature/AIRCLOSET-42497+42499                    about 1 month ago
```

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

