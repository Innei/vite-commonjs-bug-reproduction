# Vite build transform CommonJS bug reproduction

Use latest of vite (2.3.8), build for prod has some bug when transform CommonJS module.

In production runtime will crash whole application, but development mode works.

# How to reproduction

```
pnpm i 
pnpm run dev # only preview in dev
pnpm run build
vite preview
```

In dev mode, you will see this.
![](https://cdn.jsdelivr.net/gh/Innei/fancy@master/2021/0703180252.png)

But in after build and preview.

![](https://cdn.jsdelivr.net/gh/Innei/fancy@master/2021/0703180330.png)

PS: in older version vite 2.1.5, both dev and prod works.

```
vendor.da5b7916.js:1 TypeError: ea is not a function
    at Ql (vendor.da5b7916.js:7)
    at iu (vendor.da5b7916.js:7)
    at na (vendor.da5b7916.js:7)
    at yu (vendor.da5b7916.js:7)
    at Zf.Compiler (vendor.da5b7916.js:7)
    at Function.l.stringify (vendor.da5b7916.js:13)
    at vendor.da5b7916.js:13
    at vendor.da5b7916.js:13
    at o (vendor.da5b7916.js:13)
    at r (vendor.da5b7916.js:13)

```

Without miniify.

```
TypeError: one$6 is not a function
    at all$h (all.js:16)
    at root (root.js:10)
    at one$5 (one.js:37)
    at toHast$1 (index.js:122)
    at compiler (index.js:19)
    at Function.stringify2 [as stringify] (index.js:337)
    at pipelineStringify (index.js:41)
    at wrapped (wrap.js:25)
    at next (index.js:57)
    at done (wrap.js:55)
```

With source map.

```js
'use strict'

module.exports = all

var one = require('./one') // crash here. Is a commonjs transform bug?

function all(h, parent) {
  var nodes = parent.children || []
  var length = nodes.length
  var values = []
  var index = -1
  var result
  var head

  while (++index < length) {
    result = one(h, nodes[index], parent) // <--------------- one is not a function

    if (result) {
      if (index && nodes[index - 1].type === 'break') {
        if (result.value) {
          result.value = result.value.replace(/^\s+/, '')
        }

        head = result.children && result.children[0]

        if (head && head.value) {
          head.value = head.value.replace(/^\s+/, '')
        }
      }

      values = values.concat(result)
    }
  }

  return values
}


```