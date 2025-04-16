# vue-cli-service 核心功能详解

## 1. 核心功能
vue-cli-service 是 Vue CLI 提供的核心构建工具，主要提供以下功能：
- 🚀 项目脚手架快速初始化
- 🔥 开发服务器热更新（hot-reload）
- 📦 生产环境打包优化
- ⚙️ 配置管理（通过vue.config.js）

## 2. 常用命令
```bash
# 启动开发服务器
npm run serve

# 构建生产包
npm run build

# 查看webpack配置
npm run inspect
```

## 3. 插件系统工作机制
Vue CLI 通过插件体系实现功能扩展：
1. 预设配置继承（presets）
2. 自动注入webpack配置
3. 命令行命令扩展
4. UI界面集成（@vue/cli-ui）

## 4. 配置管理方式
创建 `vue.config.js` 进行自定义配置：
```javascript
module.exports = {
  devServer: {
    port: 8080,
    proxy: {
      '/api': 'http://localhost:3000'
    }
  },
  chainWebpack: config => {
    // 修改webpack配置
  }
}
```

> 提示：Vue CLI 4.x版本开始支持零配置快速启动，同时保持配置可扩展性。

## 5. 环境变量加载机制
Vue CLI 通过.env文件加载环境变量，具体规则如下：

### 文件加载顺序
1. .env.local（本地覆盖）
2. .env.[mode]（模式专用）
3. .env（全局默认）

### 模式匹配规则
- development：npm run serve 自动启用
- production：npm run build 自动启用
- test：npm run test 自动启用
- 支持自定义模式：vue-cli-service build --mode staging

### 变量命名要求
- 客户端可见变量必须以`VUE_APP_`开头
- 服务端变量无前缀要求

### 优先级示例
```bash
# 命令行 > 模式文件 > 全局文件
VUE_APP_API_URL=https://api.prod.com npm run build
```

### Webpack注入过程
1. 读取匹配的环境文件
2. 过滤VUE_APP_前缀变量
3. 通过DefinePlugin注入到客户端
4. 可通过process.env访问
```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.plugin('define').tap(args => {
      args[0]['process.env'].API_SECRET = JSON.stringify(process.env.API_SECRET)
      return args
    })
  }
}
```