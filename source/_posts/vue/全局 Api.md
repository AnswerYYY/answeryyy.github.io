---
title: 浅尝Vue3
date: 2022-3-25 11:54:57
update: 2022-3-25 11:55:00
tags:
  - Vue3
categories: [vue]
---

# 开头

Vue 团队于 2020 年 9 月 18 日晚 11 点半发布了 Vue 3.0 版本，奈何人懒，一直没有去使用。今天开始浅尝 Vue3 的新特性，以此做记录。

# Vue3.0 新特性

[Vue3 中文官网](https://v3.cn.vuejs.org/)

- 性能比 Vue2.x 快 1.2~2 倍；按需编译，体积比 vue2.x 更小
- 组合式 Api
- Teleport
- 加强 TypeScript 支持
- ...

# 安装

```bash
# 安装 vue-cli
npm install -g @vue/cli

# 创建文件夹
mkdir vue3-demo && cd vue3-demo

# 创建项目 把项目创建到当前目录
vue craete .

# 启动项目
npm run serve
```

# 全局 Api

新的全局 Api：`createApp`
在 2.X 版本中创建一个 vue 实例是通过 `new Vue()`来实现的，到了 3.X 中则是通过使用 `createApp` 这个 API 返回一个应用实例，并且可以链式调用其他的方法。

;;;createApp Vue2.x

```js
import Vue from "vue"
import App from "./App.vue"
new Vue({
	render: (h) => h(App),
}).$mount("#app")
```

;;;
;;;createApp Vue3.x

```js
import { createApp } from "vue"
import App from "./App.vue"
const app = createApp(App)
app.mount("#app")
```

;;;

# Composition Api

[中文文档](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)

## [setup](https://v3.cn.vuejs.org/guide/composition-api-setup.html#setup)

`setup`函数是一个新的组件选项。是组件`Composition Api`的入口点。它返回一个对象，对象中的所有属性是响应式数据，可直接在模板使用（类似 Vue2.x 中的 data 函数返回的对象）
`setup`函数接收两个参数 1.`props` 2.`content`

```js
<script>
export default {
  setup (props,content) {
    // ...

    return {
      // ...
    }
  }
}
</script>
```

## ref

接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象仅有一个 `.value`，指向该内部值，改变值[必须]{.red}使用其`value`属性

```html
<template>
	<div>
		<h1>{{num}}</h1>
		<button @click="add">加</button>
	</div>
</template>
<script>
	import { ref } from "vue"
	export default {
		setup() {
			const num = ref(0)
			function add() {
				num.value++
			}
			return { add, num }
		},
	}
</script>
```

## reactive

用法与`ref`类似，区别在于`reactive`返回对象的响应式副本，简而言之就是`reactive`用于复杂数据类型，例如数组和对象。`ref`用于基本数据类型,例如数字、字符串等。

```html
<template>
	<div>
		<h1>{{state.count}}</h1>
		<button @click="add">加</button>
	</div>
</template>
<script>
	import { reactive } from "vue"
	export default {
		setup() {
			const state = reactive({ count: 0 })
			function add() {
				state.count++
			}
			return { add, state }
		},
	}
</script>
```

## toRefs

将响应式对象转换为普通对象，其中结果对象的每个 property 都是指向原始对象相应 property 的`ref`。
当从组合式函数返回响应式对象时，toRefs 非常有用，这样消费组件就可以在不丢失响应性的情况下对返回的对象进行解构/展开。

`useFeatureX.js`

```js
import { reactive, toRefs } from "vue"
export function useFeatureX() {
	const state = reactive({
		foo: 1,
		bar: 2,
	})
	// 操作 state 的逻辑

	// 返回时转换为ref
	return toRefs(state)
}
```

`App.vue`

```js
export default {
	setup() {
		// 可以在不失去响应性的情况下解构
		const { foo, bar } = useFeatureX()
		return {
			foo,
			bar,
		}
	},
}
```

## computed

接受一个 getter 函数，并根据 getter 的返回值返回一个不可变的响应式 `ref` 对象。

```js
import { ref, reactive, computed } from "vue"
export default {
	setup() {
		const state = reactive({
			count: 0,
		})
		const num = ref(0)
		const stateComputed1 = computed(() => `只读计算${state.count + 2}`)
		const stateComputed2 = computed({
			get: () => {
				return `可读写计算${state.count + 4}`
			},
			set: (val) => {
				state.count = val
			},
		})

		function add() {
			state.count++
			num.value += 3
		}

		function handleClick() {
			stateComputed2.value = 999
		}
		return {
			state,
			num,
			stateComputed1,
			stateComputed2,
			add,
			handleClick,
		}
	},
}
```

## watchEffect

立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。

```js
import { ref, watchEffect } from "vue"
export default {
	setup() {
		let num = ref(0)
		watchEffect(() => {
			console.log(num.value)
			// 结果 0
		})
		setTimeout(() => {
			num.value++
			// 结果 1
		}, 100)
		return {}
	},
}
```

### 停止监听

#### 自动停止

当`watchEffect`在组件的`setup`函数或生命周期钩子被调用时，侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止。

#### 手动停止

在一些情况下，也可以显式调用返回值以停止侦听

```js
const stop = watchEffect(() => {
	/* ... */
})

// later
stop()
```

#### 清除副作用

有时副作用函数会执行一些异步的副作用，这些响应需要在其失效时清除 (即完成之前状态已改变了) 。所以侦听副作用传入的函数可以接收一个`onInvalidate`函数作入参，用来注册清理失效时的回调。当以下情况发生时，这个失效回调会被触发：

- 副作用即将重新执行时
- 侦听器被停止 (如果在 setup() 或生命周期钩子函数中使用了 watchEffect，则在组件卸载时)

```html
<template>
	<div>
		<input type="text" v-model="content" />
	</div>
</template>
<script>
	// 使用watchEffect实现输入'防抖'效果
	import { ref, watchEffect } from "vue"
	export default {
		setup() {
			const content = ref("")
			const asyncLog = (val) => {
				return setTimeout(() => {
					console.log("用户输入:", val)
				}, 1000)
			}
			watchEffect(
				(onInvalidate) => {
					//输入间隔小于一秒，清除绑定，不打印结果
					const timer = asyncLog(content.value)
					onInvalidate(() => clearTimeout(timer))
					console.log("输入值改变:", content.value)
				},
				// flush: 'pre'  watch() 和 watchEffect() 在 DOM 挂载或更新之前运行副作用，所以当侦听器运行时，模板引用还未被更新。
				// flush: 'post' 选项来定义，这将在 DOM 更新后运行副作用，确保模板引用与 DOM 保持同步，并引用正确的元素。
				{
					flush: "post", // 默认'pre'，同步'sync'，'pre'组件更新之前
				}
			)
			return { content }
		},
	}
</script>
```

<!-- ## watch
`watch` API完全等效于2.xthis.$watch（以及 `watch` 中相应的选项）。`watch`需要侦听特定的数据源，并在回调函数中执行副作用。默认情况是懒执行的，也就是说仅在侦听的源变更时才执行回调，即初始监听回调函数不执行。
`watch`接收第一个参数为数据源 -->