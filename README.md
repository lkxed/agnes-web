# Agnes AI Chat

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。界面参考主流 ChatBot 工作台：左侧会话历史，中间对话流，底部输入框；可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI API。

## 功能

- 主流 ChatBot 布局：左侧历史栏、中央欢迎态/对话流、底部固定输入框。
- 三种创作模式：聊天、图片、视频，通过输入框上方的模式胶囊切换。
- 统一图片附件：聊天和图片模式可以直接上传多张本地图片；视频模式会先把本地参考图上传到 img.scdn.io 图床，再把公开 URL 交给 Agnes 生成视频。
- 用户发送的参考图会在消息中显示为缩略图，点击缩略图会在页面内模态窗口中预览；同一条消息内有多张图时可前后切换，模态窗口内可选择新标签页打开、下载或另存为。
- 本地会话历史：自动保存标题、消息和非敏感设置，支持搜索、恢复、重命名、置顶和删除单个会话。
- 图片和视频的常用选项靠近输入框，画面比例默认“保持原比例”，也可以选择具体尺寸；视频长度会同时显示用户友好名称与具体参数。
- 高级模式：发送按钮下方可切换，开启后显示当前创作模式支持的全部 API 选项、专业字段名，并可显示或隐藏每条消息的调试详情。
- 思考模式：文本流式输出开启 Thinking 时，思考内容会显示在可展开/收起的引用块中，默认只露出 3 行。
- 取消回复：生成中发送按钮会变成停止按钮，可中止当前浏览器请求、流式输出或视频轮询；公开视频任务如果已经提交到 Agnes，当前官方文档未提供服务端取消接口，前端只能停止等待结果。
- 主题跟随系统深浅色，深色模式下接近 DeepSeek/ChatGPT 一类聊天界面。
- Agnes API Key 会和对话历史一样保存在浏览器 `localStorage`；连接状态需要点击检测或首次请求成功后才会显示“已连接”。

## 本地使用

直接用浏览器打开 `index.html` 即可。

如果浏览器对 `file://` 页面限制较多，也可以临时启动一个静态服务器：

```bash
python3 -m http.server 8080
```

然后访问 `http://localhost:8080`。

## 获取 Agnes API Key

1. 访问 [Agnes 平台登录页](https://platform.agnes-ai.com/login)，注册或登录账号。
2. 登录后进入“设置”。
3. 在设置里点击“密钥”。
4. 点击“创建新的密钥”。
5. 复制并保存好自己的密钥，然后粘贴到网页左下角的“连接 Agnes”输入框。

## 发布前检查

本项目没有构建步骤。提交前可以运行：

```bash
node -e "const fs=require('fs'); const html=fs.readFileSync('index.html','utf8'); const js=html.match(/<script>([\s\S]*)<\/script>/)[1]; new Function(js); console.log('script ok')"
git diff --check
```

确认仓库里没有真实 API Key：

```bash
git grep -n -E "sk-[A-Za-z0-9_-]{20,}|Bearer [A-Za-z0-9._-]{20,}|AIza[0-9A-Za-z_-]{20,}" -- index.html
```

## GitHub Pages 发布

1. 在 GitHub 创建一个新仓库，例如 `agnes-web`。
2. 如果还没有配置远程仓库，执行：

```bash
git remote add origin https://github.com/<user>/agnes-web.git
git branch -M main
git push -u origin main
```

如果已经配置过 `origin`，只需要：

```bash
git push
```

3. 在 GitHub 仓库页面进入 `Settings -> Pages`。
4. `Build and deployment` 里，Source 选择 `Deploy from a branch`。
5. Branch 选择 `main`，目录选择 `/ (root)`，然后保存。
6. 等待 Pages 部署完成后访问：

```text
https://<user>.github.io/agnes-web/
```

如果仓库名是 `<user>.github.io`，访问地址则是：

```text
https://<user>.github.io/
```

仓库根目录里的 `.nojekyll` 会让 GitHub Pages 按静态文件原样发布。

## API Key 安全

- 不要把真实 API Key 写进 `index.html`、README 或任何提交。
- 这个 App 是纯前端应用，用户填写的 API Key 会从浏览器直接发送到 `https://apihub.agnes-ai.com`。
- 视频本地参考图会从浏览器上传到 `https://img.scdn.io/api/v1.php`，图床返回的公开 URL 会继续发送给 Agnes 视频 API。
- 会话历史和 Agnes API Key 保存在浏览器 `localStorage`。API Key 不会写入仓库，但会保留在当前浏览器里，直到用户清除连接或清理浏览器数据。
- 本地上传的图片只在当前页面内存中用于请求，不以 Base64 形式长期保存到 `localStorage`。
- 图片下载和另存为会优先通过浏览器直接保存；另存为依赖浏览器的文件保存对话框支持。如果远程图片服务阻止跨域读取，浏览器可能无法从纯前端直接保存，需要新标签页打开后手动保存。
- 输入 API Key 后会先显示“未验证”；点击连接状态检测成功，或首次 API 请求成功后，才会显示“已连接”。
- 如果你把页面公开发布，访问者需要使用自己的 Agnes AI API Key。

## 高级说明

- 文本默认使用 `agnes-2.0-flash`；当用户添加图片或填写图片 URL 时，会自动使用支持文本+图片输入的 `agnes-1.5-flash`。
- 文本流式响应会拆分 `delta.content` 与 `delta.reasoning_content`，正文和思考过程分开渲染。
- 图片使用 `agnes-image-2.1-flash`，普通设置提供画面比例、参考图 URL 和本地参考图上传，高级设置保留结果格式。
- 视频使用 `agnes-video-v2.0`，普通设置提供画面比例、视频长度和公开参考图 URL；本地上传图片会先通过 img.scdn.io 图床换成公开 URL，高级设置保留帧数、FPS、检查次数和负面提示词。
- 本地历史使用 `agnes_chat_sessions_v1` 作为 `localStorage` key；Agnes API Key 使用 `agnes_api_key` 作为 `localStorage` key。

## API 文档

- 文本：https://agnes-ai.com/doc/agnes-20-flash
- 图像：https://agnes-ai.com/doc/agnes-image-21-flash
- 视频：https://agnes-ai.com/doc/agnes-video-v20
