# 引入 React（纯配置）

## 配置 Webpack
```shell
# Webpack 依赖
npm i -D webpack@5.72.0 webpack-cli@4.9.2 webpack-dev-server@4.8.1 html-webpack-plugin@5.5.0

# Loader
npm i -D less@4.1.2 less-loader@10.2.0 style-loader@3.3.1 css-loader@6.7.1 ts-loader@9.2.8

# React 以及业务
npm i react@17.0.2 react-dom@17.0.2 axios@0.26.1 antd@4.19.5 classnames@2.3.1
npm i -D @types/react@17.0.2 @types/react-dom@17.0.2
```

在根目录添加 `Webpack` 配置文件 `webpack.config.js`：
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  mode: 'development',
  entry: {
    index: './src/index.tsx'
  },
  module: {
    rules: [
      // 解析 TypeScript
      {
        test: /\.(tsx?|jsx?)$/,
        use: 'ts-loader',
        exclude: /(node_modules|tests)/
      },
      // 解析 CSS
      {
        test: /\.css$/i,
        use: [
          { loader: 'style-loader' },
          { loader: 'css-loader' },
        ]
      },
      // 解析 Less
      {
        test: /\.less$/i,
        use: [
          { loader: "style-loader" },
          {
            loader: "css-loader",
            options: {
              modules: {
                mode: (resourcePath) => {
                  if (/pure.css$/i.test(resourcePath)) {
                    return "pure";
                  }
                  if (/global.css$/i.test(resourcePath)) {
                    return "global";
                  }
                  return "local";
                },
              }
            }
          },
          { loader: "less-loader" },
        ],
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js', '.less', 'css'],
    // 设置别名
    alias: {
      utils: path.join(__dirname, 'src/utils/'),
      components: path.join(__dirname, 'src/components/'),
      apis: path.join(__dirname, 'src/apis/'),
      hooks: path.join(__dirname, 'src/hooks/'),
      store: path.join(__dirname, 'src/store/'),
    }
  },
  devtool: 'inline-source-map',
  // 3000 端口打开网页
  devServer: {
    static: './dist',
    port: 3000,
    hot: true,
  },
  // 默认输出
  output: {
    filename: 'index.js',
    path: path.resolve(__dirname, 'dist'),
    clean: true,
  },
  // 指定模板 html
  plugins: [
    new HtmlWebpackPlugin({
      template: './public/index.html',
    }),
  ],
};
```

在 public/index.html 添加模板 HTML 文件：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>React App</title>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```
在 `package.json` 添加启动命令：

```json
{
  "scripts": {
    "start": "webpack serve",
    "test": "jest"
  }
}

```

## 添加入口
现在来实现我们的 React App。在 src/index.tsx 添加入口：
```tsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import "antd/dist/antd.css";

ReactDOM.render(<App />, document.querySelector("#root"));
```