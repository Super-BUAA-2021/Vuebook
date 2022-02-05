# npm install -save/-g 参数的区别

## 特征

### npm install X

* 会把 X 包安装到 node_modules 目录中
* 不会修改 package.json
* 运行 `npm install` 命令时不会自动安装 X

### npm install X -save

* 会把 X 包安装到 node_modules 目录中
* 会在 package.json 的 `dependencies` 属性下添加 X
* 执行 `npm install` 命令时，会自动安装 X 到 node_modules 目录中
* 执行 `npm install –production` 或者注明 `NODE_ENV` 变量值为 `production` 时，会自动安装 X 到 node_modules 目录中

### npm install X -save-dev

* 会把 X 包安装到 node_modules 目录中
* 会在 package.json 的 `devDependencies` 属性下添加 X
* 执行 `npm install` 命令时，会自动安装 X 到 node_modules 目录中
* 执行 `npm install –production` 或者注明 `NODE_ENV` 变量值为 `production` 时，不会自动安装 X 到node_modules目录中

### npm install X -g

* 安装模块到全局，不会在项目 node_modules 目录中保存模块包
* 不会将模块依赖写入 `devDependencies` 或 `dependencies` 节点
* 运行 `npm install` 初始化项目时不会自动安装模块

## `-save` 和 `-save-dev` 的区别

`devDependencies` 中的包是在开发中用到的，发布后是找不到的；而 `dependencies` 中的包是开发和线上都需要用到的。

## 使用原则

* 项目框架或脚手架，比如 vue、vue-cli 等可以安装到全局，使用 `-g`；
* 对于项目依赖的包，仅开发时用到、发布部署后无需使用的，采用 `-save-dev`，否则使用 `-save`。

> 拿捏不好就用 `-save`