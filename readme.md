# 前端开发中的 CSS

本文梳理了 CSS 技术在前端开发中的演变路径，主要脉络包括：「ink → Bootstrap → 预处理器 → Tailwind → UnoCSS」。重点拆解 Tailwind 和 UnoCSS 如何在构建阶段高效生成所需原子样式。

---

## 第一部分：link 与 class

步骤只有两步：

1. 在 `<head>` 里手动写 link 标签
2. 每做一个按钮设置 class

```html
<link rel="stylesheet" href="css/base.css" />
<link rel="stylesheet" href="css/button.css" />
<style>
	.row {
		width: 100%;
	}
</style>
```

在不同的 HTML 文件中引入 CSS 文件和自定义 style，样式混用，维护成本较高。

---

## 第二部分：Bootstrap 语义化 class

Bootstrap 把常用组件全部预制成 `.btn`、`.btn-primary`、`.row`、`.col-md-6` 等语义化 class，规范了样式定义并提升开发效率，但也带来样式重叠和权重问题：

```html
<div class="container">
	<div class="row">
		<div class="col-md-6 offset-md-3">
			<button class="btn btn-primary btn-lg w-100">提交</button>
		</div>
	</div>
</div>
```

---

## 第三部分：预处理器 Sass/LESS + Loader

预处理器带来了变量、嵌套、mixin 等能力：

```scss
// styles/mixins.scss
@mixin button-variant($bg, $color: #fff) {
	background: $bg;
	color: $color;
	&:hover {
		background: darken($bg, 10%);
	}
}
.btn-primary {
	@include button-variant(#007bff);
}
```

通过 webpack 的链式 loader，把 SCSS 转成浏览器可识别的 CSS：

```js
module.exports = {
	module: {
		rules: [{ test: /\.scss/, use: ['style-loader', 'css-loader', 'sass-loader'] }]
	}
}
```

本质上仍是「先写一大堆样式，再整体打包」，优化了开发效率，但输出 CSS 文件体积较大。

---

## 第四部分：TailwindCSS 的 JIT 原子化引擎

Tailwind 在构建阶段启动 JIT（Just-in-Time）引擎，只扫描代码中真正出现的 class 字符串，立刻生成对应原子 CSS，输出到 dist/index.css，运行时不再做任何扫描。

**运行逻辑：**

1. 扫描：读取 `tailwind.config.js` 的 content 字段
2. 匹配：正则提取所有静态/任意值/组合变体类名
3. 生成：每匹配一个 class，立刻生成原子 CSS 并交给 PostCSS 管道

**用法示例：**

-   Vite/Webpack/PostCSS

```sh
npm i -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

入口 CSS：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

-   CDN 方式

```html
<script src="https://cdn.tailwindcss.com"></script>
```

> 浏览器每遇到新 class 就向云端请求对应 CSS 片段，原理仍是「扫描 → 生成」，但禁止用于生产环境。

**常见误区：**

-   运行时拼接类名如 `bg-{color}-500` 不生效
-   构建时扫描不到完整字符串
-   解决方案：使用任意值 `bg-[#3b82f6]` 或 CSS 变量

---

## 第五部分：UnoCSS 的即时原子化引擎

UnoCSS 在构建时通过「正则 + 规则函数」即时翻译模板中的每一个原子 token 为最小 CSS，未扫描到则不生成，体积极小。

**运行逻辑：**

1. 扫描：读取 `uno.config.ts` 的 content 文件
2. 分词：正则拆 class 字符串成数组
3. 匹配：遍历 rules，命中即输出原子 CSS，支持 HMR 缓存

**核心能力：**

-   零配置预设：开箱即用 Tailwind/Windi 语法
-   自定义原子规则：一行正则即可扩展
-   高级特性：图标、属性化、暗黑模式、CSS 变量等

**最小可运行代码：**

-   Vite 启用：

```sh
npm i -D unocss
```

```js
// vite.config.ts
import UnoCSS from 'unocss/vite'
export default { plugins: [UnoCSS()] }
```

入口 CSS：

```css
@import 'uno.css';
```

自定义规则：

```js
import { defineConfig, presetUno } from 'unocss'
export default defineConfig({
	presets: [presetUno()],
	rules: [
		[/^m-(\d+)/, ([, d]) => ({ margin: d * 0.25 + 'rem' })],
		[/^text-#([0-9a-f]{3,8})/i, ([, c]) => ({ color: '#' + c })]
	],
	shortcuts: { btn: 'px-4 py-2 rounded inline-block' }
})
```

模板使用：

```html
<div class="text-#ff6600 btn m-10">按钮</div>
```

构建后只生成：

```css
.m-10 {
	margin: 2.5rem;
}
.text-\#ff6600 {
	color: #ff6600;
}
.btn {
	/*...*/
}
```

---

## 第六部分：Tailwind vs UnoCSS 差异一览

| 对比项       | TailwindCSS     | UnoCSS          |
| ------------ | --------------- | --------------- |
| 生成时机     | 扫描 → JIT 生成 | 扫描 → 即时生成 |
| 预设集合     | 内置几千条      | 完全按需，可空  |
| 自定义难度   | 需 plugin API   | 一行正则即可    |
| 构建速度     | 秒级            | 毫秒级          |
| 动态类名支持 | 不支持          | 不支持          |
| 图标方案     | 需额外插件      | 一行配置即可    |

---

## 第七部分：总结与参考链接

link 到今天原子化，CSS 的抽象层级一路下降，灵活度一路上升。原子化 CSS 让样式管理变得更高效和可控。

-   [Tailwind 官方文档](https://tailwindcss.com/docs/just-in-time-mode)
-   [UnoCSS 官方文档](https://unocss.dev)
-   [作者 AntFu 深度文章](https://antfu.me/posts/reimagine-atomic-css-zh)
