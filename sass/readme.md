# Sass 预编译 CSS 使用说明

本目录用于存放 Sass 预编译 CSS 的相关文件。Sass 是一种 CSS 预处理器，可以让样式开发更加高效和模块化。Sass 文件通常以 `.scss` 或 `.sass` 结尾，编译后生成标准的 `.css` 文件。

## 编译 Sass 的常用方式

### 1. 编辑器插件

许多主流编辑器（如 VS Code、WebStorm）都支持 Sass 插件，能够自动监控并编译 Sass 文件。

**示例：**

-   安装 VS Code 插件 “Live Sass Compiler”
-   在编辑器中保存 `.scss` 文件后，插件自动生成对应的 `.css` 文件

### 2. 命令行工具（CLI）

可以通过 Sass 官方 CLI 工具进行编译。

**安装 Sass CLI：**

```bash
npm install -g sass
```

**编译示例：**

```bash
sass input.scss output.css
```

或监听文件变化自动编译：

```bash
sass --watch input.scss:output.css
```

### 3. Webpack 集成

在前端项目中，可以通过 Webpack 配合 `sass-loader` 实现自动编译。

**安装依赖：**

```bash
npm install sass-loader sass webpack --save-dev
```

**Webpack 配置示例：**

```js
module.exports = {
	module: {
		rules: [
			{
				test: /\.scss$/,
				use: ['style-loader', 'css-loader', 'sass-loader']
			}
		]
	}
}
```

---

根据项目需求选择合适的编译方式，提升开发效率。
