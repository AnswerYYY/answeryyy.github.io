---
title: 折叠展开动画
date: 2022-11-2 17:02:09
update: 2022-11-2 17:02:11
tags:
  - 动画
  - 展开
categories: 
- [Vue, Vue2]
---

# 简介

  :::info
  -  此源码来自于[Element UI](https://element.eleme.cn/#/)
  :::
  一个折叠展开动画组件，应用场景多为需要展开更多数据时使用。[例如展开更多搜索条件]{.blue}
 

# 组件源码
``` javascript
const elTransition = '0.3s height ease-in-out, 0.3s padding-top ease-in-out, 0.3s padding-bottom ease-in-out'
const Transition = {
  'before-enter' (el) {
    el.style.transition = elTransition
    if (!el.dataset) el.dataset = {}

    el.dataset.oldPaddingTop = el.style.paddingTop
    el.dataset.oldPaddingBottom = el.style.paddingBottom

    el.style.height = 0
    el.style.paddingTop = 0
    el.style.paddingBottom = 0
  },

  'enter' (el) {
    el.dataset.oldOverflow = el.style.overflow
    if (el.scrollHeight !== 0) {
      el.style.height = el.scrollHeight + 'px'
      el.style.paddingTop = el.dataset.oldPaddingTop
      el.style.paddingBottom = el.dataset.oldPaddingBottom
    } else {
      el.style.height = ''
      el.style.paddingTop = el.dataset.oldPaddingTop
      el.style.paddingBottom = el.dataset.oldPaddingBottom
    }

    el.style.overflow = 'hidden'
  },

  'after-enter' (el) {
    el.style.transition = ''
    el.style.height = ''
    el.style.overflow = el.dataset.oldOverflow
  },

  'before-leave' (el) {
    if (!el.dataset) el.dataset = {}
    el.dataset.oldPaddingTop = el.style.paddingTop
    el.dataset.oldPaddingBottom = el.style.paddingBottom
    el.dataset.oldOverflow = el.style.overflow

    el.style.height = el.scrollHeight + 'px'
    el.style.overflow = 'hidden'
  },

  'leave' (el) {
    if (el.scrollHeight !== 0) {
      el.style.transition = elTransition
      el.style.height = 0
      el.style.paddingTop = 0
      el.style.paddingBottom = 0
    }
  },

  'after-leave' (el) {
    el.style.transition = ''
    el.style.height = ''
    el.style.overflow = el.dataset.oldOverflow
    el.style.paddingTop = el.dataset.oldPaddingTop
    el.style.paddingBottom = el.dataset.oldPaddingBottom
  }
}

export default {
  name: 'collapseTransition',
  functional: true,
  render (h, { children }) {
    const data = {
      on: Transition
    }
    return h('transition', data, children)
  }
}

```
# 使用方法
```html
<template>
  <div>
    <collapse-transition>
      <div v-if="show"> 
        自定义展开内容
      </div>
    </collapse-transition>
  </div>
</template>
<script>
  import collapseTransition from "collapseTransition.js"
  export default {
    components:{
      collapseTransition
    },
    data () {
      return {
        show: false
      }
    }
  }
</script> 
```