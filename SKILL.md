---
name: "doubao-web"
description: "使用 Playwright 托管浏览器的方式，调用豆包 Web 端生图功能。当用户要求使用豆包画图、生成图片时调用此技能。"
instructions: |
  1. 当用户请求画图、生成图片，并指明使用豆包或未指定特定工具时，请调用此技能。
  2. 提取用户描述中的核心主体、风格和场景作为 prompt。
  3. 如果用户指定了图片比例（如头像、壁纸、16:9等），请自动匹配并添加 `--ratio=<value>` 参数。
  4. 如果用户指定了图片保存路径，请使用 `--output=<path>` 参数。
  5. 默认使用后台无头模式执行命令：`npx ts-node /Users/pengjianfang/skills/doubao-web-image/scripts/main.ts "用户的prompt" [可选参数]`。
  6. 执行完毕后，向用户展示生成的图片路径或确认生成成功。
---

# Doubao Web Image Generator

这个项目/技能通过 Playwright 自动化控制浏览器的方式，直接利用豆包 Web 端的真实环境进行图片生成，从而完美避开 `a_bogus` 签名风控问题。

## 功能

- 自动保存登录状态（在 `~/.doubao-web-session`）
- 在真实浏览器环境中发送生图 Prompt
- 拦截并解析 SSE 流式响应，获取无水印原图 URL

## 如何运行

```bash
# 默认使用无头模式 (后台静默运行) 和 获取原图画质，并默认保存为 generated.png
npx ts-node scripts/main.ts "一只赛博朋克风格的猫咪"

# 指定图片保存路径 (--output 或 --image)
npx ts-node scripts/main.ts "一只赛博朋克风格的猫咪" --output="./my_cyber_cat.png"

# 指定图片画质 (--quality=preview 或 --quality=original)
# preview 抓取速度更快，original 尝试获取大图画质 (默认)
npx ts-node scripts/main.ts "一只赛博朋克风格的猫咪" --quality=preview --output="./preview_cat.png"

# 首次运行或需要登录时，必须使用 --ui 参数显示浏览器窗口
npx ts-node scripts/main.ts "测试" --ui
```

### 命令行参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `prompt` | (必填) 生成图片的提示词 | `一只可爱的金毛犬` |
| `--output=<path>` / `--image=<path>` | 图片下载保存的本地路径 | `./generated.png` |
| `--quality=<value>` | 图片画质要求，可选 `preview` (预览图) 或 `original` (高清原图) | `original` |
| `--ratio=<value>` | 图片比例选择，支持：`1:1` (正方形头像), `2:3` (社交媒体自拍), `3:4` (经典比例拍照), `4:3` (文章配图插画), `9:16` (手机壁纸人像), `16:9` (桌面壁纸风景) | |
| `--ui` | 显示浏览器界面（首次登录时必须使用） | 后台静默运行 |
| `--help`, `-h` | 显示帮助菜单 | |

## 技术原理

1. **浏览器托管**: 利用 Playwright 启动一个真实的 Chromium 浏览器，加载用户数据目录。
2. **UI 自动化**: 定位输入框，自动填入 `帮我生成图片：{prompt}` 并模拟回车。
3. **网络拦截**: 监听 `/samantha/chat/completion` 的 POST 请求响应，获取完整的 SSE 数据流。
4. **数据解析**: 使用正则匹配响应流中的 `image_ori` 的 URL。

## 目录结构

- `scripts/doubao-webapi/client.ts` - 核心 Playwright 客户端逻辑
- `scripts/main.ts` - 命令行入口文件
- `package.json` - 项目依赖
