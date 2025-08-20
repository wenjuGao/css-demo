# less 预编译 CSS 使用说明

本目录用于存放基于 [Less](http://lesscss.org/) 预编译的 CSS 文件。Less 是一种动态样式语言，可以扩展 CSS 的功能，如变量、嵌套、混合等。

## 编译 less 的方式

### 1. 编辑器插件

许多主流编辑器（如 VS Code、WebStorm）支持 less 插件，保存 `.less` 文件时自动编译为 `.css`。

**示例流程：**

-   安装 less 编译插件（如 VS Code 的 "Easy LESS"）。
-   编辑并保存 `style.less`，自动生成 `style.css`。

### 2. CLI 命令行工具

可以使用 less 官方命令行工具进行编译。

**安装 less：**

```bash
npm install -g less
```

**编译命令：**

```bash
lessc styles.less styles.css
```

### 3. Webpack 集成

在前端项目中，可通过 webpack 配合 `less-loader` 实现自动编译。

**安装依赖：**

```bash
npm install less less-loader --save-dev
```

**webpack 配置示例：**

```js
module.exports = {
	module: {
		rules: [
			{
				test: /\.less$/,
				use: ['style-loader', 'css-loader', 'less-loader']
			}
		]
	}
}
```

## 参考

-   [Less 官方文档](http://lesscss.org/)
-   [less-loader 文档](https://webpack.js.org/loaders/less-loader/)
-   编辑器插件市场
