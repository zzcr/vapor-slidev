---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://images.unsplash.com/photo-1461749280684-dccba630e2f6?auto=format&fit=crop&w=1500&q=80
# some information about your slides (markdown enabled)
title: Vue 3.6 Vapor模式介绍
info: |
  ## Vue 3.6 Vapor模式专题
  介绍Vapor模式、VDOM模式区别、优缺点、开启方式及JSX Vapor用法
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
# open graph
# seoMeta:
#  ogImage: https://cover.sli.dev
---

# Vue 3.6 Vapor模式介绍

## 什么是Vapor模式？

Vapor模式是Vue 3.6引入的一种全新渲染策略，跳过虚拟DOM（VDOM），直接生成操作真实DOM的高效代码。

- 只需在\<script setup\> 标签上加 vapor 属性即可开启
- 兼容现有的Composition API和模板语法
- 让Vue性能媲美Solid.js等信号类框架

<!-- 这里介绍了Vapor模式的基本概念和定位 -->

---

## Vapor模式与VDOM模式的区别

| 特性         | VDOM模式                | Vapor模式                |
| ------------ | ----------------------- | ------------------------ |
| 渲染机制     | 虚拟DOM diff+patch      | 编译期生成直接DOM操作代码 |
| 内存占用     | 较高                    | 更低                     |
| 首屏包体积   | 较大                    | 更小                     |
| 响应式追踪   | 依赖VNode树             | 精确追踪依赖节点         |
| 性能         | 已很优秀                | 更快，极致优化           |
| 兼容性       | 支持所有Vue特性         | 部分特性暂不支持         |

<!-- 通过表格直观对比两种模式的核心差异 -->

---

## VDOM模式优缺点

- 优点：
  - 兼容性强，支持所有Vue特性（如Transition、KeepAlive等）
  - 可以抽象出VNode树，对接自定义渲染器
  - 生态成熟，第三方库支持好
- 缺点（大型项目可能表现的更突出）：
  - 存在VNode创建和diff的运行时开销
  - 内存占用相对更高

---

## Vapor模式优缺点

- 优点：
  - 极致性能，包体积和内存占用大幅下降
  - 响应式追踪更精准，更新更快
  - 适合性能敏感场景和新项目
- 缺点（可能是缺点~~~）：
  - 不支持 Options API
  - 暂时不支持Transition、KeepAlive、Suspense、SSR等高级特性
  - 明确不支持的特性：
    - 选项 API
    - app.config.globalProperties
    - getCurrentInstance()nullVapor 组件中的回报
    - 隐式实例属性（例如`$slots`和）`$props`在 Vapor 模板表达式中不可用
    - @vue:xxx每个元素的生命周期事件

---

## 如何开启Vapor模式？

### 局部开启（推荐渐进迁移）

```vue
<script setup vapor>
import { ref } from 'vue'
const count = ref(0)
</script>
<template>
  <button @click="count++">Count: {{ count }}</button>
</template>
```

### 全局开启（新项目/极致性能）

- 使用`createVaporApp`替换`createApp`
- 或将根组件命名为`App.vapor.vue`
- Vite配置需升级到vapor专用插件

```js
import { createVaporApp } from 'vue/vapor'
import App from './App.vue'
createVaporApp(App).mount('#app')
```

<!-- 展示如何在不同层级启用Vapor模式 -->

---

## Vapor模式的适用建议

- ✅ 性能关键页面（如首页、列表页）
- ✅ 新项目可直接全局启用
- ✅ 局部渐进迁移，兼容VDOM组件
- ❌ 暂不建议迁移全部老项目
- ❌ SSR、Transition、KeepAlive等场景暂不推荐
- ❌ 深度依赖VDOM生态的库需谨慎

<!-- 给出实际开发中的最佳实践建议 -->

---

## Vue JSX Vapor 用法简介

Vue JSX Vapor是专为Vapor模式优化的JSX编译器插件，支持高性能的JSX写法。

- 安装：`npm i vue-jsx-vapor`
- Vite/webpack/rollup等主流构建工具均支持
- 兼容大部分Vue内置指令和宏

```js
// vite.config.ts
import VueJsxVapor from 'vue-jsx-vapor/vite'
export default {
  plugins: [VueJsxVapor()]
}
```

<!-- 介绍JSX Vapor的安装和基本用法 -->

---

## Vue JSX Vapor 指令与宏

### 常用指令支持

- v-show、v-if、v-for、v-model等均可直接在JSX中使用
- 语法示例：

```jsx
<input v-model={val} />
<input v-show={visible} />
```

### 宏用法

- 支持大部分Vue宏，如defineProps、defineEmits等
- 适合TypeScript类型推导和IDE智能提示

### 自定义指令

- 新版指令接收getter函数，返回清理函数

```js
const MyDirective = (el, valueGetter) => {
  watchEffect(() => {
    el.textContent = valueGetter()
  })
  return () => console.log('clean when uninstalling')
}
```

<!-- 展示JSX Vapor下指令和宏的典型用法 -->

---

# 总结

- Vapor模式让Vue性能大幅提升，适合新项目和性能敏感场景
- 支持局部和全局渐进迁移，兼容VDOM生态
- Vue JSX Vapor让JSX写法也能享受Vapor极致性能
- 未来Vapor模式将持续完善，建议关注官方动态

<!-- 总结全文，便于回顾和后续查阅 -->
