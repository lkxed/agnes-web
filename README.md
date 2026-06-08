# Agnes AI Chat

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。界面参考主流 ChatBot 工作台：左侧会话历史，中间对话流，底部输入框；可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI API。

## 功能

- 主流 ChatBot 布局：左侧历史栏、中央欢迎态/对话流、底部固定输入框。
- 三种创作模式：写作、图片、视频，通过输入框上方的模式胶囊切换。
- 本地会话历史：自动保存标题、消息和非敏感设置，支持搜索、恢复和删除单个会话。
- 图片和视频的常用选项靠近输入框，高级参数默认折叠。
- 主题跟随系统深浅色，深色模式下接近 DeepSeek/ChatGPT 一类聊天界面。
- API Key 仅保存在 `sessionStorage`。关闭浏览器标签页后会清空，不会写入仓库或本地历史。

## 本地使用

直接用浏览器打开 `index.html` 即可。

如果浏览器对 `file://` 页面限制较多，也可以临时启动一个静态服务器：

```bash
python3 -m http.server 8080
```

然后访问 `http://localhost:8080`。

## GitHub Pages 发布

1. 在 GitHub 创建一个新仓库，例如 `agnes-web`。
2. 推送本仓库代码：

```bash
git remote add origin https://github.com/<user>/agnes-web.git
git branch -M main
git push -u origin main
```

3. 在 GitHub 仓库设置里打开 `Settings -> Pages`。
4. Source 选择 `Deploy from a branch`，Branch 选择 `main` 和 `/root`。
5. 发布后访问 `https://<user>.github.io/agnes-web/`。

## API Key 安全

- 不要把真实 API Key 写进 `index.html`、README 或任何提交。
- 这个 App 是纯前端应用，用户填写的 API Key 会从浏览器直接发送到 `https://apihub.agnes-ai.com`。
- 会话历史保存在浏览器 `localStorage`，只包含对话内容、模式和非敏感设置，不保存 API Key。
- API Key 保存在浏览器 `sessionStorage`，关闭标签页后需要重新连接 Agnes。
- 如果你把页面公开发布，访问者需要使用自己的 Agnes AI API Key。

## 高级说明

- 文本使用 `agnes-2.0-flash`，高级设置里保留创意程度、回答长度、流式输出、Thinking 和图片 URL 输入。
- 图片使用 `agnes-image-2.1-flash`，普通设置提供画面比例和参考图，高级设置保留结果格式。
- 视频使用 `agnes-video-v2.0`，普通设置提供画面比例、视频长度和参考图；高级设置保留帧数、FPS、检查次数和负面提示词。
- 本地历史使用 `agnes_chat_sessions_v1` 作为 `localStorage` key；API Key 使用 `agnes_api_key` 作为 `sessionStorage` key。

## API 文档

- 文本：https://agnes-ai.com/doc/agnes-20-flash
- 图像：https://agnes-ai.com/doc/agnes-image-21-flash
- 视频：https://agnes-ai.com/doc/agnes-video-v20
