# Doubao Web Image Generation CLI

基于 Playwright 的豆包 (Doubao) Web 端网页自动化生图工具。

🔗 **GitHub Repository:** [pjf6568/doubao-web-image](https://github.com/pjf6568/doubao-web-image)

## ⚠️ 免责声明 / Disclaimer

**本项目仅供编程学习、Playwright 自动化测试研究和技术交流使用。**
- 本项目并非豆包官方产品，与字节跳动公司无任何关联。
- 使用本项目产生的任何后果由使用者本人承担。
- **请勿将本项目用于任何非法、侵权、恶意刷量或商业牟利的场景。**
- 若因使用本工具导致账号封禁、功能受限或其他损失，作者不承担任何责任。
- 请自觉遵守相关平台的用户服务协议及生成内容规范。

---

## 🌟 特性

- 🤖 **免 API Key**：通过 Playwright 模拟浏览器操作，直接复用网页版登录状态。
- 🖼️ **高清大图下载**：自动拦截原生下载链接，获取 >4MB 的无损高分辨率原图，而非缩略图。
- 📏 **比例控制**：支持通过自然语言参数自动拼接控制图片长宽比（如 `16:9`, `1:1`）。
- 🛡️ **验证码自动降级**：默认无头 (Headless) 模式运行，遇到风控拦截时自动弹窗切换到 UI 模式供人工验证。

## 📦 安装与配置

确保你已经安装了 Node.js (建议 v18+) 和 npm。

### 方式一：直接全局安装（推荐）

你可以直接通过 npm 从 GitHub 全局安装此工具，安装后可以在任意目录直接使用 `doubao-image` 命令：

```bash
npm install -g git+https://github.com/pjf6568/doubao-web-image.git
```
*(注意：首次运行可能需要执行 `npx playwright install chromium` 来安装浏览器内核)*

### 方式二：克隆到本地运行

```bash
# 1. 克隆项目
git clone https://github.com/pjf6568/doubao-web-image.git
cd doubao-web-image

# 2. 安装项目依赖
npm install

# 3. 安装 Playwright 浏览器内核
npx playwright install chromium
```

## 🚀 使用方法

如果你使用了**全局安装**，可以将下面所有的 `npx ts-node scripts/main.ts` 替换为简单的 `doubao-image` 命令。

### 1. 首次使用（需手动登录）
由于项目需要获取你的登录 Cookie，第一次运行**必须带上 `--ui` 参数**以打开可视化浏览器：

```bash
npx ts-node scripts/main.ts "画一只可爱的猫咪" --ui
```
*在弹出的浏览器中完成手机号/验证码登录后，脚本会自动检测到输入框并继续生成图片。登录态会保存在本地的 `~/.doubao-web-session` 目录中，后续无需重复登录。*

### 2. 日常生图（后台无头模式）
登录成功后，可以直接在后台静默生成并下载图片：

```bash
npx ts-node scripts/main.ts "一只带有未来科技感的机器狗"
```

### 3. 高级参数

- `--quality=<value>`：图片质量，可选 `preview`（预览图）或 `original`（原始大图，默认）。
- `--ratio=<value>`：图片比例选择。支持的比例及推荐场景如下：
  - `1:1`：正方形头像
  - `2:3`：社交媒体自拍
  - `3:4`：经典比例拍照
  - `4:3`：文章配图插画
  - `9:16`：手机壁纸人像
  - `16:9`：桌面壁纸风景
- `--output=<path>` 或 `--image=<path>`：指定图片下载保存的路径。
- `--ui`：强制显示浏览器界面（用于重新登录或手动过验证码）。

## 🤖 作为 AI Skill 使用 (For AI Assistants)

本项目内置了 `SKILL.md`，非常适合作为大模型（如 Trae 等 AI 助手）的扩展技能。

1. **导入技能**：AI 助手只需读取项目根目录下的 `SKILL.md` 文件。
2. **自然语言交互**：用户可以直接对 AI 说：“帮我用豆包画一张赛博朋克猫咪的手机壁纸”。
3. **自动执行**：AI 会根据 `SKILL.md` 中的指令，自动解析出 `prompt="赛博朋克猫咪"` 和 `--ratio=9:16`，并在后台静默调用命令行生成图片返回给用户。

**综合示例：**
```bash
npx ts-node scripts/main.ts "星空下的赛博朋克城市" --ratio="9:16" --quality=original --output=./city_wallpaper.png
```

## 🐛 常见问题

- **Q: 提示“未能获取到图片，可能触发了人机验证”？**
  A: 脚本已内置自动重试机制。当在无头模式下遇到风控，脚本会自动关闭并以 UI 模式重启，给你 120 秒的时间在弹出的浏览器中手动完成滑块或点选验证码。
- **Q: 生成的图片大小只有几百 KB？**
  A: 确保没有加上 `--quality=preview` 参数。脚本默认会模拟点击大图并寻找下载按钮来获取 `image_pre_watermark` 级别的高清无损原图（通常 >1MB）。
