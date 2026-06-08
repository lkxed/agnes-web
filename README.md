# Agnes AI Chat

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。界面参考主流 ChatBot 工作台：左侧会话历史，中间对话流，底部输入框；可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI API。

## 功能

- 主流 ChatBot 布局：左侧历史栏、中央欢迎态/对话流、底部固定输入框。
- 三种创作模式：写作、图片、视频，通过输入框上方的模式胶囊切换。
- 统一图片附件：写作、图片、视频模式都可以用同一个“添加图片”按钮上传多张本地图片。
- 用户发送的参考图会在消息中显示为缩略图，点击可以预览原图。
- 本地会话历史：自动保存标题、消息和非敏感设置，支持搜索、恢复和删除单个会话。
- 图片和视频的常用选项靠近输入框，画面比例默认“保持原比例”，也可以选择具体尺寸；视频长度会同时显示用户友好名称与具体参数。
- 高级模式：发送按钮下方可切换，开启后显示当前创作模式支持的全部 API 选项、专业字段名，并可显示或隐藏每条消息的调试详情。
- 思考模式：文本流式输出开启 Thinking 时，思考内容会显示在可展开/收起的引用块中，默认只露出 3 行。
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
- 本地上传的图片只在当前页面内存中用于请求，不以 Base64 形式长期保存到 `localStorage`。
- API Key 保存在浏览器 `sessionStorage`，关闭标签页后需要重新连接 Agnes。
- 如果你把页面公开发布，访问者需要使用自己的 Agnes AI API Key。

## 高级说明

- 文本默认使用 `agnes-2.0-flash`；当用户添加图片或填写图片 URL 时，会自动使用支持文本+图片输入的 `agnes-1.5-flash`。
- 文本流式响应会拆分 `delta.content` 与 `delta.reasoning_content`，正文和思考过程分开渲染。
- 图片使用 `agnes-image-2.1-flash`，普通设置提供画面比例、参考图 URL 和本地参考图上传，高级设置保留结果格式。
- 视频使用 `agnes-video-v2.0`，普通设置提供画面比例、视频长度、参考图 URL 和本地参考图上传；高级设置保留帧数、FPS、检查次数和负面提示词。
- 本地历史使用 `agnes_chat_sessions_v1` 作为 `localStorage` key；API Key 使用 `agnes_api_key` 作为 `sessionStorage` key。

## API 文档

- 文本：https://agnes-ai.com/doc/agnes-20-flash
- 图像：https://agnes-ai.com/doc/agnes-image-21-flash
- 视频：https://agnes-ai.com/doc/agnes-video-v20
