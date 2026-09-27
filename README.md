# Agnes AI Chat

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。界面参考主流 ChatBot 工作台：左侧会话历史，中间对话流，底部输入框；可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI API。

## 功能

- 主流 ChatBot 布局：左侧历史栏、中央欢迎态/对话流、底部固定输入框。
- 三种创作模式：聊天、图片、视频，通过输入框上方的模式胶囊切换。
- 统一图片附件：上传的图片会跟随当前输入框，切换聊天、图片、视频模式后仍然可用；视频模式会先把本地参考图上传到 img.scdn.io 图床，再把公开 URL 交给 Agnes 生成视频。
- 用户发送的参考图会在消息中显示为缩略图，点击缩略图会在页面内模态窗口中预览；同一条消息内有多张图时可前后切换，模态窗口内可选择新标签页打开、下载或另存为。
- 本地会话历史：自动保存标题、消息和非敏感设置，支持搜索、恢复、重命名、置顶和删除单个会话。
- 消息操作：可以引用自己或 Agnes 的任意消息，引用会随新消息一起发送并显示为引用块；被引用消息里的图片或视频也会保留预览。也可以编辑自己已发送的消息，发送后会从该消息开始重新生成后续回复。
- 图片和视频的常用选项靠近输入框：图片先选 `1K / 2K / 3K / 4K` 尺寸档位，再选 `1:1`、`16:9`、`21:9` 等画面比例，页面会实时显示对应的输出像素；视频固定 720P，通过画面比例决定横竖屏，时长可选 4 到 12 秒。
- 视频生成方式可选「文生视频」「首尾帧」「参考图 / 音频」：选项面板会跟着切换对应的输入框。选择会以你为准，素材缺失时发送前就会提示；只有默认的「文生视频」会按你填的图片自动升级，不会让素材被悄悄忽略。
- 选项面板：一个“选项”按钮集中管理当前模式的设置，所有文案保持用户友好。
- 思考：聊天模式开启“思考”后会自动使用流式输出，思考内容显示在可展开/收起的引用块中，默认只露出 3 行；不开启时使用普通非流式回复。
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

## 技术说明

- 文本使用 `agnes-3.0-flash`。同一个模型既支持纯文本，也支持「文本 + 图像 URL」输入，所以不再按“是否带图”切换模型；上下文窗口 `512K`，单次最多输出 `65,536` tokens。
- 文本流式响应会拆分 `delta.content` 与 `delta.reasoning_content`，正文和思考过程分开渲染；开启「思考」时会带上 `chat_template_kwargs.enable_thinking = true` 并自动切换为流式输出。
- 图片使用 `agnes-image-2.5-flash`。请求必带 `prompt` 和 `size`（`1K` / `2K` / `3K` / `4K` 档位），再配合 `ratio` 指定画面比例；返回格式只在 `extra_body.response_format` 里指定（`url` 或 `b64_json`），顶层不放 `response_format`，也不要传 `tags: ["img2img"]`。图生图和多图合成通过 `extra_body.image` 传入参考图，前 3 张参考图不额外计费。
- 视频使用 `agnes-video-2.5-flash`。创建任务用 `POST /v1/videos`，查询用官方推荐的 `GET /agnesapi?video_id=...&model_name=agnes-video-2.5-flash`（`task_id` 路径仅作兼容兜底，每 2 秒轮询一次）。`size` 固定为字符串 `"720P"`，画面由 `aspect_ratio` 决定，`seconds` 支持 `"4"` 到 `"12"`。
- 视频生成方式由 `mode` 控制：`text` 只用文字；`keyframe` 用 `first_frame` / `last_frame` 控制起止画面（至少一张）；`reference` 用 `images`（最多 5 张）和 `audios`（最多 3 段）保持角色与风格，该模型不支持参考视频。
- 生成方式以你的选择为准：选「首尾帧」需要至少一张首帧或尾帧图片，选「参考图 / 音频」需要至少一项参考图或参考音频，缺少素材时会在发送前给出提示，不会发出接口一定会拒绝的请求。只有默认的「文生视频」会按你实际填写的图片自动升级为「首尾帧」或「参考图 / 音频」，避免素材被悄悄忽略。参考图超过 5 张、参考音频超过 3 段时会自动截断并提示。
- 多个参考图的 URL 输入框使用多行文本域：每行一个 URL，也可以用逗号分隔（普通单行输入框会把换行直接吃掉，导致多个 URL 粘成一个）。
- 本地历史使用 `agnes_chat_sessions_v1` 作为 `localStorage` key；Agnes API Key 使用 `agnes_api_key` 作为 `localStorage` key。

## API 文档

- 文档索引：https://wiki.agnes-ai.com/llms.txt
- 文本 Agnes 3.0 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-30-flash
- 图像 Agnes Image 2.5 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-image-25-flash
- 视频 Agnes Video 2.5 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-video-25-flash
- 模型定价：https://wiki.agnes-ai.com/zh-Hans/docs/pricing
