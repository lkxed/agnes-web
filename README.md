# Agnes AI Chat

一个零构建、纯 HTML 的 Agnes AI 对话式 Web App。界面参考主流 ChatBot 工作台：左侧会话历史，中间对话流，底部输入框；可以直接部署到 GitHub Pages，通过浏览器调用 Agnes AI API。

## 功能

- 主流 ChatBot 布局：左侧历史栏、中央欢迎态/对话流、底部固定输入框。
- 三种创作模式：聊天、图片、视频，通过输入框上方的模式胶囊切换。
- 每种模式都可以自行切换 Agnes 模型：聊天可在 `agnes-3.0-flash`、`agnes-2.5-flash`、`agnes-2.5-pro-beta`、`agnes-2.5-pro` 之间切换（上下文窗口 512K 或 1M），图片可选 Image 2.0 / 2.1 / 2.5 Flash，视频可选 `agnes-video-2.5-flash`（固定 720P）或 `agnes-video-2.5`（最高 2K）。切换模型后，尺寸、清晰度等选项会自动收敛到该模型真正支持的取值，选择随会话保存。
- 统一图片附件：上传的图片会跟随当前输入框，切换聊天、图片、视频模式后仍然可用；视频模式会先把本地参考图上传到 img.scdn.io 图床，再把公开 URL 交给 Agnes 生成视频。
- 文本附件（聊天模式）：可以直接选本地的 `.txt` / `.md` / `.csv` / 代码等纯文本文件，最多 4 个、合计不超过 20 万字。文件显示为输入框上方的「文本附件」芯片，可以同时写指令，发送时内容随消息一起提交，消息里保留文件名和字数。
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
- 文本附件的正文不会上传到任何服务，只在浏览器内读出并拼进请求的提示词；会话历史里只保留前 20,000 字，超出部分不落盘。
- 图片下载和另存为会优先通过浏览器直接保存；另存为依赖浏览器的文件保存对话框支持。如果远程图片服务阻止跨域读取，浏览器可能无法从纯前端直接保存，需要新标签页打开后手动保存。
- 输入 API Key 后会先显示“未验证”；点击连接状态检测成功，或首次 API 请求成功后，才会显示“已连接”。
- 如果你把页面公开发布，访问者需要使用自己的 Agnes AI API Key。

## 技术说明

- 文本可选四个模型：`agnes-3.0-flash`、`agnes-2.5-flash`、`agnes-2.5-pro-beta`、`agnes-2.5-pro`。四者共用同一套请求结构（`text` 与 `image_url` 内容块、`chat_template_kwargs.enable_thinking`），单次最多输出 `65,536` tokens；差别在上下文窗口（3.0 / 2.5 Flash 为 `512K`，2.5 Pro 与 Pro Beta 为 `1M`）和价格。`agnes-2.5-pro` 按 Pro 档价位计费，`agnes-2.5-pro-beta` 按 Beta 价位计费，两个 Flash 模型当前免费。
- 文本流式响应会拆分 `delta.content` 与 `delta.reasoning_content`，正文和思考过程分开渲染；开启「思考」时会带上 `chat_template_kwargs.enable_thinking = true` 并自动切换为流式输出。
- 图片可选 `agnes-image-2.5-flash`、`agnes-image-2.1-flash`、`agnes-image-2.0-flash`。2.5 与 2.1 的接入方式完全一致：请求必带 `prompt` 和档位 `size`（`1K` / `2K` / `3K` / `4K`），再配合 `ratio` 指定画面比例；`response_format` 只放在 `extra_body` 内（`url` 或 `b64_json`），顶层不放，也不要传 `tags: ["img2img"]`。图生图和多图合成通过 `extra_body.image` 传入参考图，前 3 张参考图不额外计费。
- `agnes-image-2.0-flash` 的文档把 `size` 写成精确像素（如 `1024x768`）且没有 `ratio` 参数，所以选中它时页面会把档位与比例换算成精确像素串再提交（例如 `2K` + `16:9` → `2624x1472`），并且不发送 `ratio`；界面上的「将输出」提示会标出这一差异。
- 视频可选 `agnes-video-2.5-flash` 与 `agnes-video-2.5`。创建任务用 `POST /v1/videos`，查询用官方推荐的 `GET /agnesapi?video_id=...&model_name=<所选模型>`（`task_id` 路径仅作兼容兜底，每 2 秒轮询一次）。轮询会带上创建任务时所用的模型名，所以中途切换模型也不会查错任务。
- 两个视频模型的限制不同：`agnes-video-2.5-flash` 的 `size` 固定为 `"720P"`，参考图最多 5 张、参考音频最多 3 段，且不支持参考视频；`agnes-video-2.5` 的 `size` 可选 `"720P"` / `"1080P"` / `"1K"` / `"2K"`，参考图最多 8 张。`1K` 档位是固定 `1024x1024` 方形，选中它时页面会禁用「画面比例」并且不发送 `aspect_ratio`。`seconds` 两个模型都支持字符串 `"4"` 到 `"12"`。
- `aspect_ratio` 同为 `720P` 时，两个模型的输出像素并不相同（例如 `21:9` 在 `agnes-video-2.5` 是 `1470x630`，在 `agnes-video-2.5-flash` 是 `1680x720`），页面会按所选模型显示真实输出尺寸。
- 视频生成方式由 `mode` 控制：`text` 只用文字；`keyframe` 用 `first_frame` / `last_frame` 控制起止画面（至少一张）；`reference` 用 `images` 和 `audios` 保持角色与风格。参考视频（`videos`，仅 `agnes-video-2.5` 支持）目前没有在界面上开放。
- 生成方式以你的选择为准：选「首尾帧」需要至少一张首帧或尾帧图片，选「参考图 / 音频」需要至少一项参考图或参考音频，缺少素材时会在发送前给出提示，不会发出接口一定会拒绝的请求。只有默认的「文生视频」会按你实际填写的图片自动升级为「首尾帧」或「参考图 / 音频」，避免素材被悄悄忽略。参考图超过所选模型的上限（2.5 Flash 为 5 张，2.5 为 8 张）或参考音频超过 3 段时会自动截断并提示。
- 多个参考图的 URL 输入框使用多行文本域：每行一个 URL，也可以用逗号分隔（普通单行输入框会把换行直接吃掉，导致多个 URL 粘成一个）。
- 文本附件同样只在浏览器内处理：Agnes 文本接口只接受 `text` 和 `image_url` 两种内容块，没有文件上传接口，所以文件内容是在浏览器里读出后按「文本附件 → 我的问题」的顺序直接拼进提示词的。解码先按 UTF-8，出现替换字符时再回退到 GBK（Windows 上保存的老 `.txt` 常见编码），带 BOM 的 UTF-16 也会识别；含空字节的文件按二进制跳过。单文件上限 2MB，超出或空文件会在添加时提示并跳过。
- 会话历史使用 `localStorage` 保存（约 5MB 上限）：单个文本附件正文最多保存前 20,000 字，超出部分只存在于当前内存中；重新编辑这类消息时会提示需要重新选择文件。写入失败时页面会提示清理旧对话，而不是静默丢失。
- 本地历史使用 `agnes_chat_sessions_v1` 作为 `localStorage` key；Agnes API Key 使用 `agnes_api_key` 作为 `localStorage` key。

## API 文档

- 文档索引：https://wiki.agnes-ai.com/llms.txt
- 文本 Agnes 3.0 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-30-flash
- 文本 Agnes 2.5 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-25-flash
- 文本 Agnes 2.5 Pro：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-25-pro
- 文本 Agnes 2.5 Pro Beta：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-25-pro-beta
- 图像 Agnes Image 2.5 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-image-25-flash
- 图像 Agnes Image 2.1 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-image-21-flash
- 图像 Agnes Image 2.0 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-image-20-flash
- 视频 Agnes Video 2.5：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-video-25
- 视频 Agnes Video 2.5 Flash：https://wiki.agnes-ai.com/zh-Hans/docs/agnes-video-25-flash
- 模型定价：https://wiki.agnes-ai.com/zh-Hans/docs/pricing
