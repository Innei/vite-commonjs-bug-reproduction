<template>
  <article v-html="text"></article>
</template>

<script lang="ts">
import gfm from 'remark-gfm'
import html from 'remark-html'
import markdown from 'remark-parse'
import unified from 'unified'
import { defineComponent } from 'vue'

const parser = unified()
  .use(markdown)
  .use(gfm)
  .use(html, {
    handlers: {
      spoiler: (h, node) => {
        return h(
          node,
          'del',
          {
            class: 'spoiler',
          },
          [
            {
              type: 'text',
              value: node.value,
            },
          ],
        )
      },
    },
  })
export default defineComponent({
  name: 'App',
  setup() {
    const md = `
This is markdown.

# Vue 3 & Vite
    `
    return {
      text: parser().processSync(md).toString(),
    }
  },
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
