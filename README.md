# Live Countdown - 现代化计时器应用

致力于成为最漂亮的计时器应用。

Live Countdown 是一个具有现代化 UI 和交互的计时器网页应用，支持多计时器管理、数据分享、用户数据同步、全屏展示等功能，可进行倒计时、正计时和显示世界时钟。采用玻璃态设计和流畅动画效果，提供极佳的视觉体验。

预览: [Live Countdown](https://livecountdown.cc/)

![image](https://raw.ravelloh.top/20250323/image.2obow0upmh.webp)
![image](https://raw.ravelloh.top/20250323/image.7zqlgqhqss.webp)
![image](https://raw.ravelloh.top/20250323/image.26ln7ftxqc.webp)
![image](https://raw.ravelloh.top/20250323/image.9nzydxa4pj.webp)
![image](https://raw.ravelloh.top/20250323/image.83a7egcmsx.webp)
![image](https://raw.ravelloh.top/20250323/image.9dd4kru4xb.webp)
![image](https://raw.ravelloh.top/20250323/image.51ebd8a4jh.webp)
![image](https://raw.ravelloh.top/20250323/image.6wqw5umcp8.webp)
![image](https://raw.ravelloh.top/20250323/image.6f0zcpvm6r.webp)

## 特点

### 核心功能
- 支持多个计时器的创建和管理
- 数据本地存储与云端同步
- 支持生成分享链接和二维码
- 支持全屏展示模式
- 智能识别常用节假日
- 支持PWA，可安装到主屏幕并离线使用
- 国际化支持，提供中英双语
- 支持倒计时通知提醒

### 视觉和交互
- 精美的玻璃态设计和流畅动效
- 自适应模糊渐变背景，随主题色变化
- 暗色/亮色主题自动切换
- 完全响应式设计，适配各种设备

## 技术栈

- Next.js - React 框架
- Framer Motion - 高级动画库
- Tailwind CSS - 实用工具优先的 CSS 框架
- localStorage - 本地数据持久化
- [KV Cache](https://github.com/RavelloH/kv-cache) - 云端数据存储服务

### 本地运行

1. 克隆仓库
```bash
git clone 
```

2. 安装依赖
```bash
npm install
```

3. 启动开发服务器
```bash
npm run dev
```

4. 构建生产版本
```bash
npm run build
```

## 使用说明

- 点击右下角"+"按钮创建新的倒计时
- 点击左下角分享按钮分享倒计时
- 点击右上角菜单切换主题或编辑计时器
- 点击右上角全屏按钮进入全屏模式
- 向下滚动可查看更多信息和运行日志
- 在设置中生成同步ID，用于在不同设备间同步数据

## 许可证

MIT License
