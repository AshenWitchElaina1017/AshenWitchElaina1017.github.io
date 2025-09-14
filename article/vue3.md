---

title: Vue 3

date: 2025-08-31

summary: 新一代渐进式 JavaScript 框架。通过组合式 API (Composition API)、Proxy 响应式系统和 Vite 工具链，提供了前所未有的开发体验、性能和代码组织能力。

image: https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202509101118191.jpg

---


# Vue 3 速览：更强大、更灵活的下一代框架

Vue 3 是对广受欢迎的 JavaScript 框架 Vue.js 的一次彻底重写和进化。它在保留了 Vue 2 平易近人、语法简洁的优点的基础上，引入了全新的**组合式 API (Composition API)**，并从底层用 `Proxy` 重构了响应式系统，带来了显著的性能提升、更强的 TypeScript 支持以及更佳的大型项目代码组织能力。结合现代化的构建工具 Vite，Vue 3 为开发者提供了极致的开发体验。

## 核心特性

  - **组合式 API (Composition API)**：一种全新的、更灵活的逻辑组织和复用模式，解决了大型组件中逻辑分散的问题。
  - **性能飞跃**：
      - **更快的渲染**：通过静态树提升（Static Tree Hoisting）和优化的虚拟 DOM Diff 算法，渲染速度显著提升。
      - **更小的体积**：通过摇树优化（Tree-shaking）有效减少打包体积。
      - **更高效的响应式系统**：基于 `Proxy` 实现，解决了 Vue 2 中 `Object.defineProperty` 的一些限制。
  - **Vite 驱动的开发体验**：默认采用 Vite 作为构建工具，提供毫秒级的服务启动和热模块更新（HMR），开发效率极高。
  - **一流的 TypeScript 支持**：整个核心库使用 TypeScript 重写，提供了无与伦比的类型推断和开发时类型检查。
  - **新的内置组件**：提供了 `<Teleport>`、`<Suspense>` 等强大的新功能，用于处理渲染到 DOM 外部、异步组件等场景。
  - **Pinia 状态管理**：官方推荐的新一代状态管理库，更轻量、更简单，且拥有完美的 TypeScript 支持。

## 深度解析：组合式 API (Composition API)

组合式 API 是 Vue 3 最具变革性的新特性。它允许我们根据**逻辑功能**来组织代码，而不是像过去的选择式 API (Options API) 那样强制按 `data`、`methods`、`computed` 等选项来分割。

### 推荐语法: `<script setup>`

在 Vue 3.2+ 版本中，官方推荐使用 `<script setup>` 语法糖来编写组合式 API，它极大地简化了代码。

  - **更简洁**：无需 `setup()` 函数和 `return` 语句，所有顶级变量和函数都自动暴露给模板。
  - **更直观**：组件导入后可直接在模板中使用，代码更易于阅读和维护。

#### 示例：一个计数器组件

```vue
<script setup lang="ts">
// 1. 导入响应式 API
// ref 用于创建基本类型的响应式数据
// computed 用于创建计算属性
import { ref, computed } from 'vue';

// 2. 声明响应式状态
// 使用 ref() 创建一个响应式变量 `count`，初始值为 0
const count = ref(0);

// 3. 声明方法
// 定义一个函数，用于修改响应式状态
function increment() {
  // 访问或修改 ref 创建的变量时，需要通过 .value 属性
  count.value++;
}

// 4. 声明计算属性
// computed 会根据依赖的响应式变量自动更新
const doubleCount = computed(() => count.value * 2);
</script>

<template>
  <div>
    <p>当前计数值: {{ count }}</p>
    <p>双倍计数值: {{ doubleCount }}</p>

    <button @click="increment">增加</button>
  </div>
</template>
```

## 核心响应式 API

在 `<script setup>` 中，我们主要使用以下几个核心函数来创建和管理组件的状态。

### 1\. `ref`

用于创建任何类型值的响应式引用，包括基本类型（`string`, `number`）和对象。在 JS/TS 中访问其值需要通过 `.value` 属性。

```ts
// 导入 ref 函数
import { ref } from 'vue';

// 创建一个字符串类型的响应式引用
const message = ref('Hello, Vue 3!');

// 修改值
message.value = '你好，Vue 3！';
```

### 2\. `reactive`

仅用于创建**对象**（或数组）的响应式代理。它会“深度”地将对象内的所有属性都变为响应式。访问其属性时**不需要** `.value`。

```ts
// 导入 reactive 函数
import { reactive } from 'vue';

// 创建一个响应式对象
const state = reactive({
  user: {
    name: 'Elaina',
    level: 1,
  },
});

// 直接修改属性即可触发更新
state.user.level++; 
```

> **何时使用 `ref` vs `reactive`？**
>
>   - **推荐**：优先使用 `ref`。它对于所有类型都适用，使得代码风格更加统一。
>   - **`reactive`**：当你需要处理一个复杂的、深层嵌套的对象，并且不希望到处使用 `.value` 时，可以使用 `reactive`。但请注意，对 `reactive` 对象进行解构或重新赋值会使其失去响应性。

### 3\. `watch` & `watchEffect`

用于侦听和响应数据的变化。

  - **`watch`**：精确地侦听一个或多个特定的数据源。它默认是“懒执行”的（只在数据变化后执行），并能访问到变化前后的值。

<!-- end list -->

```ts
// 导入 watch 和 ref
import { watch, ref } from 'vue';
const question = ref('');

// 侦听 question 的变化
watch(question, (newValue, oldValue) => {
  console.log(`问题从 '${oldValue}' 变为 '${newValue}'`);
});
```

  - **`watchEffect`**：立即执行一次其回调函数，并自动追踪函数内所有响应式依赖。任何依赖发生变化，该函数会重新执行。

<!-- end list -->

```ts
// 导入 watchEffect 和 ref
import { watchEffect, ref } from 'vue';
const userID = ref(1);

// userID 变化时，这个函数会自动重新执行
watchEffect(async () => {
  const response = await fetch(`https://api.example.com/users/${userID.value}`);
  console.log(await response.json());
});
```

## 为什么选择 Vue 3

  - **平易近人，上手迅速**：保持了 Vue 经典的核心思想和优秀的官方文档，对于新手和从 Vue 2 迁移的开发者都非常友好。
  - **极致的开发体验**：Vite 带来了闪电般的速度，`<script setup>` 语法让代码编写更流畅，强大的 TypeScript 支持则保证了代码的健壮性。
  - **无与伦比的可维护性**：组合式 API 从根本上解决了大型组件逻辑混杂的问题，使得代码更易于阅读、重构和跨组件复用。
  - **性能卓越**：经过优化的渲染引擎和响应式系统，让 Vue 3 成为了构建高性能 Web 应用的可靠选择，无论是简单的着陆页还是复杂的单页应用（SPA）。