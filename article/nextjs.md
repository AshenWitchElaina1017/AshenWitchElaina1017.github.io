---

title: Next.js

date: 2025-09-11

summary: 基于 React 的全栈框架，支持 RSC、SSR、SSG、ISR 与强大的路由与优化能力。

image: https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202507280817040.jpg

---

# Next.js 速览：下一代 Web 开发框架

Next.js 是一个基于 React 的开源全栈 Web 框架，由 Vercel 开发。它在 React 的基础上，提供了一整套为生产环境而生的解决方案，包括但不限于：强大的路由系统（App Router）、灵活的渲染策略（RSC, SSR, SSG, ISR）、内置的性能优化（图片、字体、脚本）以及创建 API 的能力。这使得开发者可以专注于业务逻辑，同时轻松构建出高性能、SEO 友好且用户体验卓越的现代 Web 应用。

## 核心特性

  - **App Router**：基于文件系统的路由，支持嵌套布局、加载状态（Loading UI）、错误处理（Error UI）等，结构清晰，功能强大。
  - **React Server Components (RSC)**：默认的组件渲染模式，将组件的渲染工作放在服务端，减少客户端 JavaScript 体积，提升首屏加载速度。
  - **多种渲染策略**：按需为不同页面选择最合适的渲染方式，如 SSR、SSG、ISR，在性能与灵活性之间取得完美平衡。
  - **内置优化**：开箱即用的图片、字体和脚本优化组件，可显著改善核心 Web 指标（Core Web Vitals）。
  - **Route Handlers**：在服务端轻松创建、读取和修改数据的 API 路由。
  - **一流的 TypeScript 支持**：内置 TypeScript 配置，提供更好的类型检查和代码自动补全。

## 渲染策略深度解析

Next.js 的强大之处在于其灵活的渲染机制，让你可以为应用中的每个页面选择最优策略。

### 1\. React Server Components (RSC) - 服务端组件

这是 App Router 中的默认模式。组件在服务端执行，其代码**不会**被打包进客户端的 JavaScript 文件中。

  - **优势**：
      - **减少客户端负载**：显著减小前端包体积，加快页面加载和交互速度。
      - **直接访问后端资源**：可以在组件内直接访问数据库、文件系统或内部 API，无需额外创建 API 路由。
      - **安全性**：敏感数据和逻辑（如 API 密钥）可以安全地保留在服务端。

<!-- end list -->

```tsx
// app/page.tsx
// 这是一个默认在服务端运行的 React Server Component
// 它可以直接使用 async/await 获取数据

/**
 * 获取文章列表
 * @returns {Promise<Array<{id: number, title: string}>>} 返回文章对象数组的 Promise
 */
async function getPosts() {
  // 直接在组件中发起请求，这段代码仅在服务端运行
  const res = await fetch('https://api.example.com/posts');
  if (!res.ok) {
    throw new Error('Failed to fetch data');
  }
  return res.json();
}

/**
 * 首页组件
 * @returns {JSX.Element} 返回渲染的 JSX 元素
 */
export default async function HomePage() {
  // 调用数据获取函数
  const posts = await getPosts();

  // 渲染文章列表
  return (
    <main>
      <h1>文章列表</h1>
      <ul>
        {posts.map((post: { id: number, title: string }) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </main>
  );
}
```

> 如果需要在组件中使用 `useState`、`useEffect` 等 Hooks 或添加浏览器事件监听，你需要在文件顶部添加 `"use client";` 指令，将其标记为客户端组件（Client Component）。

### 2\. SSR (Server-Side Rendering) - 服务端渲染

页面在**每次请求时**在服务端动态生成 HTML。

  - **适用场景**：需要展示高度个性化、实时性强的内容，如用户仪表盘、社交媒体信息流。
  - **实现方式**：在页面中使用了动态函数（如 `cookies()` 或 `headers()`）或设置了不缓存数据的 `fetch` 请求。

<!-- end list -->

```ts
// app/dashboard/page.tsx
import { cookies } from 'next/headers';

/**
 * 仪表盘页面，演示动态服务端渲染
 * @returns {JSX.Element} 返回渲染的 JSX 元素
 */
export default function DashboardPage() {
  // 从请求中读取 cookie，这将使页面动态渲染
  const cookieStore = cookies();
  const user = cookieStore.get('user')?.value;

  // 根据用户信息渲染个性化内容
  return <h1>欢迎, {user || '访客'}!</h1>;
}
```

### 3\. SSG & ISR - 静态与增量生成

  - **SSG (Static Site Generation)**：页面在**构建时**生成一次 HTML。这是默认的数据获取策略。

  - **ISR (Incremental Static Regeneration)**：在构建时生成静态页面，并配置一个刷新周期，在用户访问时定期在后台重新生成页面。

  - **适用场景**：内容相对稳定，更新不频繁但需要极高性能和 SEO 的页面，如博客文章、产品文档、营销页面。

<!-- end list -->

```ts
// app/posts/[slug]/page.tsx

/**
 * 获取单篇文章数据，并配置 ISR
 * @param {string} slug - 文章的唯一标识
 * @returns {Promise<object>} 返回文章对象的 Promise
 */
async function getPost(slug: string) {
  // fetch 默认会缓存数据，实现 SSG
  // 通过 next.revalidate 选项可以开启 ISR，单位为秒
  const res = await fetch(`https://api.example.com/posts/${slug}`, {
    next: { revalidate: 3600 }, // 每 3600 秒（1小时）重新生成一次
  });
  return res.json();
}

/**
 * 文章详情页
 * @param {{ params: { slug: string } }} props - 包含动态路由参数的对象
 * @returns {Promise<JSX.Element>} 返回渲染的 JSX 元素的 Promise
 */
export default async function PostPage({ params }: { params: { slug: string } }) {
  const post = await getPost(params.slug);
  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </article>
  );
}
```

## 后端能力：Route Handlers

除了渲染页面，你还可以使用 Route Handlers 轻松创建 API 接口。只需在 `app` 目录下创建 `route.ts` (或 `.js`) 文件即可。

```ts
// app/api/hello/route.ts
import { NextResponse } from 'next/server';

/**
 * 处理 GET 请求的函数
 * @param {Request} request - 传入的请求对象
 * @returns {NextResponse} 返回一个 JSON 响应
 */
export async function GET(request: Request) {
  // 返回一个 JSON 格式的响应
  return NextResponse.json({ message: 'Hello, World!' });
}

/**
 * 处理 POST 请求的函数
 * @param {Request} request - 传入的请求对象
 * @returns {Promise<NextResponse>} 返回一个 JSON 响应的 Promise
 */
export async function POST(request: Request) {
  // 解析请求体中的 JSON 数据
  const data = await request.json();
  // 返回处理后的数据
  return NextResponse.json({ received: data });
}
```

## 为何选择 Next.js

  - **卓越的开发体验**：基于文件系统的路由直观易懂，结合热模块替换（Fast Refresh）等功能，编码调试流畅高效。
  - **极致的性能**：通过 RSC 减少客户端 JavaScript，配合自动代码分割、图片优化等机制，轻松获得优异的 Core Web Vitals 分数。
  - **全栈合一**：前端渲染和后端 API 开发在同一个框架、同一个代码库中完成，简化了技术栈，提高了开发效率。
  - **强大的生态和社区**：由 Vercel 支持，拥有庞大且活跃的社区，以及丰富的文档、教程和第三方库。是构建严肃生产级应用的首选框架之一。