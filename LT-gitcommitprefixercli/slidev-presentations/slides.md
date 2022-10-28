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

# cliをつくる

oclif, execa(, inquirer)でオリジナルコマンドをつくろう

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

# コード


```ts {all|5}
  async run(): Promise<void> {
    const { flags } = await this.parse(Commit);
    
    try {
      const result = await this.commit("test commit2", "b");
      this.log(result);
    } catch (e) {
      this.errorMessage(e);
    }
  }
```

<arrow v-click="3" x1="400" y1="420" x2="230" y2="330" color="#564" width="3" arrowSize="1" />

[^1]: [Learn More](https://sli.dev/guide/syntax.html#line-highlighting)

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

---

# コード


```ts {all|2-4|8}
  private async commit(title: string, message?: string) {
    const currentBranchName = await this.getCurrentBranchName();
    const commitMessage = `${currentBranchName}${divider}${title}`;
    const commitPayload = ["commit", "-m", commitMessage];
    if (message) {
      commitPayload.push("-m", message);
    }
    const { stdout } = await execa("git", commitPayload);
    return stdout;
  }

  private async getCurrentBranchName() {
    const { stdout, stderr } = await execa("git", [
      "rev-parse",
      "--abbrev-ref",
      "HEAD",
    ]);
    if (stderr) throw new Error(stderr);
    return stdout;
  }

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

---

# oclif

* heroku(SalesForce)が作っているTypeScript製cli作成ツール
  * https://github.com/oclif/oclif
* execa, inquirerを併用することで、コマンドを実行するオリジナルコマンドが作れる

---

# execa

> This package improves child_process methods

* 最適化しつつコマンド実行してくれるJSライブラリ
* https://github.com/sindresorhus/execa

# inquirer
* 応答式のコマンドを作成できるJSライブラリ
* https://github.com/SBoudrias/Inquirer.js

--- 

kaurtaさんのコマンド + brunoさんのgitmoji-cを参考に
ブランチ名を自動でくっつけてくれるgit commit代替コマンドをつくる
