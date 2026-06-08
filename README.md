# Agnes AI Console

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。它可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI 的文本、图像和视频 API。

## 功能

- 文本：调用 `agnes-2.0-flash`，支持普通聊天、流式输出、Thinking 开关和图片 URL 输入。
- 图像：调用 `agnes-image-2.1-flash`，支持文生图、图生图、URL 输出和 Base64 输出。
- 视频：调用 `agnes-video-v2.0`，支持文本生成视频、图片生成视频、多图/关键帧输入，并自动轮询任务结果。
- API Key 仅保存在 `sessionStorage`。关闭浏览器标签页后会清空，不会写入仓库。

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
- 如果你把页面公开发布，访问者需要使用自己的 Agnes AI API Key。

## API 文档

- 文本：https://agnes-ai.com/doc/agnes-20-flash
- 图像：https://agnes-ai.com/doc/agnes-image-21-flash
- 视频：https://agnes-ai.com/doc/agnes-video-v20
