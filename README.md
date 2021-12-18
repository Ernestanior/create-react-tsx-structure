## 整合最新版搭建react tsx框架
### create-react-app, typescript, ant-design, less/sass, less-loader/sass-loader一整套流程配置

### 版本说明
1. create-react-app版本: 5.0.0 （通过 ```npm info create-react-app``` 命令进行版本查询)
2. ant-design版本: 4.16.13 (通过package.json文件查询)
3. less版本: 3.12.2 (通过package.json文件查询)
4. less-loader版本: 7.0.2 (通过package.json文件查询)
5. npm版本: 8.3.0 （通过 ```npm -v``` 命令进行版本查询)

### 一. 用create-react-app脚手架安装项目

#### 用npm或者yarn都可以
```npx create-react-app test1 —template typescript```

### 二. 按需引入ant-design

#### 1. 安装antd
```npm add --save antd```
#### 2. 安装按需引入插件
```npm add react-app-rewired customize-cra
npm add babel-plugin-import ```
#### 3. 修改package.json
把原来的
```
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  }
```
改为
```
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-scripts eject"
  }
```
#### 4. 在项目文件目录下(与package.json同级目录)添加config-overrides.js文件,内容如下:
```
const { override, fixBabelImports } = require("customize-cra");

module.exports = override(
  fixBabelImports("import", {
    libraryName: "antd",
    libraryDirectory: "es",
    style: "css",
  })
);
```

### 三. 配置绝对路径，为之后文件的嵌套引入做铺垫，这里有两种配置方式，均以src作为根目录：
#### 第一种
##### 在tsconfig.json中代码:
```
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```
##### 配置完成之后，如果想引入src下的文件，直接写文件名即可，举例如下:
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/e39652c7610d2ca57a74773d71832690fa75ddf0/screenshot/s4.png" width="220px"/>
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/e39652c7610d2ca57a74773d71832690fa75ddf0/screenshot/s5.png" width="320px"/>

#### 第二种,以符号@作为src目录的绝对路径
##### 使用customize-cra来覆写webpack底层配置(不清楚customize-cra用法的可以去github上customize-cra库查看说明，Ant Design官网也有相关的配置说明)
##### 重写方式一
```
const { override } = require('customize-cra');
const path = require("path");
 
module.exports = override(
     config =>{
         config.resolve.alias = {
             "@": path.resolve(__dirname, "src")
         };
         return config;
     },
);
```
##### 重写方式二
```
const { override, addWebpackAlias } = require('customize-cra');
const path = require("path");
 
module.exports = override(
    addWebpackAlias({
        ["@"]: path.resolve(__dirname, "src")
    }),
);
```
##### 配置完成之后，用符号@表示根目录src，如果想引入src下的文件，举例如下:
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/e39652c7610d2ca57a74773d71832690fa75ddf0/screenshot/s6.png" width="320px"/>

### 四. vsCode中配置@绝对路径的智能路径提示
#### 1. 安装vsCode插件：Path Intellisence (插件库查找并下载)
#### 2. vsCode中setting.json配置插件功能：（打开vscode编辑器设置，在设置中打开setting.json文件）
```
// 配置@路径智能提示
  "path-intellisense.mappings": {
    "@": "${workspaceRoot}/src"
  }
```
#### 3. 在tsconfig.json中添加以下配置：
```
{
  "compilerOptions": {
      "baseUrl": "./",
      "paths": {
        "@/*": ["src/*"]
      }
  }
}
```
### 五. 引入less/scss样式(这里的less和scss都需用低版本来兼容react，不然运行不起来)

#### 1. 引入scss(如果用less可以跳过该步骤)
先安装低版本sass: ```npm add sass-loader@7.3.1 node-sass```
后在 node_modules/react-scripts/config/webpack.config.js里加上以下代码
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/c615bc74a1a3c999d2a1a04c6970138453ac69fc/screenshot/s2.png" width="620px"/>
#### 2. 引入less
安装低版本less和less-loader: ```npm I less@3.12.2 less-loader@7.1.0```
然后在 node_modules/react-scripts/config/webpack.config.js里
1) 找到css顶部配置,添加less配置
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/c615bc74a1a3c999d2a1a04c6970138453ac69fc/screenshot/s3.png" width="420px"/>
2) 在文件500行之后找到与下图类似的代码，仿照css的配置复制并修改成less，代码如下
<image src="https://github.com/Ernestanior/create-react-tsx-structure/blob/c615bc74a1a3c999d2a1a04c6970138453ac69fc/screenshot/s1.png" width="520px"/>

