# ai-Resume

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VS Code](https://code.visualstudio.com/) + [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Recommended Browser Setup

- Chromium-based browsers (Chrome, Edge, Brave, etc.):
  - [Vue.js devtools](https://chromewebstore.google.com/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)
  - [Turn on Custom Object Formatter in Chrome DevTools](http://bit.ly/object-formatters)
- Firefox:
  - [Vue.js devtools](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)
  - [Turn on Custom Object Formatter in Firefox DevTools](https://fxdx.dev/firefox-devtools-custom-object-formatters/)

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## AI Assistant API Configuration

本项目使用智谱AI免费API（glm-4-flash模型）提供AI助手功能。

### 配置步骤：

1. **获取智谱API Key**
   - 访问 [智谱AI开放平台](https://open.bigmodel.cn/)
   - 注册并登录账号
   - 在控制台创建API Key

2. **配置环境变量**
   ```sh
   # 复制示例配置文件
   cp .env.example .env
   
   # 编辑 .env 文件，填入你的真实 API Key
   VITE_ZHIPU_API_KEY=your_actual_api_key_here
   ```

3. **安全注意事项**
   - ⚠️ `.env` 文件已加入 `.gitignore`，不会被提交到Git仓库
   - ⚠️ 不要将API Key硬编码在代码中
   - ⚠️ 不要将`.env`文件分享给他人
   - ✅ 生产环境建议通过服务器端代理调用API，避免前端暴露密钥

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```
