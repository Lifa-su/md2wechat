<p align="center">
  <h1 align="center">📝 MD2WeChat</h1>
  <p align="center">
    <strong>Markdown to WeChat Official Account Formatter</strong><br>
    <em>将 Markdown 一键转换为微信公众号排版，复制即用</em>
  </p>
</p>

<p align="center">
  <a href="https://github.com/Lifa-su/md2wechat/stargazers"><img src="https://img.shields.io/github/stars/Lifa-su/md2wechat?style=flat-square&logo=github&color=yellow" alt="Stars"></a>
  <a href="https://github.com/Lifa-su/md2wechat/network/members"><img src="https://img.shields.io/github/forks/Lifa-su/md2wechat?style=flat-square&logo=github&color=blue" alt="Forks"></a>
  <a href="https://github.com/Lifa-su/md2wechat/issues"><img src="https://img.shields.io/github/issues/Lifa-su/md2wechat?style=flat-square&logo=github&color=green" alt="Issues"></a>
  <a href="https://github.com/Lifa-su/md2wechat/blob/main/LICENSE"><img src="https://img.shields.io/github/license/Lifa-su/md2wechat?style=flat-square&color=purple" alt="License"></a>
  <a href="https://lifa-su.github.io/md2wechat"><img src="https://img.shields.io/badge/demo-live-brightgreen?style=flat-square&logo=googlechrome" alt="Live Demo"></a>
</p>

<p align="center">
  <a href="https://lifa-su.github.io/md2wechat">🎯 Live Demo / 在线体验</a>
</p>

---

## 📸 Screenshot / 截图

<!-- Replace with actual screenshot -->
<p align="center">
  <img src="https://via.placeholder.com/800x450.png?text=MD2WeChat+Screenshot" alt="MD2WeChat Screenshot" width="800">
</p>

> 💡 *Left: Markdown editor with dark theme — Right: Real-time WeChat-style preview*

---

## ✨ Features / 功能亮点

- ⚡ **Real-time Preview** — Write Markdown on the left, see WeChat-formatted output instantly on the right
- 🎨 **4 Built-in Themes** — Minimal / Tech / Literary / Business — switch with one click
- 🖍️ **Syntax Highlighting** — Powered by highlight.js, supports all major programming languages
- 📋 **One-click Rich Text Copy** — Auto-inlines CSS styles, paste directly into WeChat editor without losing formatting
- 📐 **Draggable Split Pane** — Resize editor and preview panels by dragging the divider
- 📝 **Full Markdown Support** — Headings, lists, tables, blockquotes, images, links, strikethrough, and more
- 🔒 **100% Client-side** — Pure frontend, zero server uploads, your content stays on your device
- 🌐 **Zero Install** — No build tools, no dependencies to install — just open and use

---

## 🚀 Quick Start / 快速开始

### Option 1: Use Online (Recommended)

👉 **[https://lifa-su.github.io/md2wechat](https://lifa-su.github.io/md2wechat)**

### Option 2: Run Locally

```bash
git clone https://github.com/Lifa-su/md2wechat.git
cd md2wechat

# Simply open in browser
open index.html        # macOS
# or
xdg-open index.html   # Linux
# or
start index.html       # Windows
```

### Option 3: Local Server

```bash
cd md2wechat
python3 -m http.server 8080
# Visit http://localhost:8080
```

### 📖 Usage / 使用流程

1. ✍️ Write or paste Markdown in the left editor
2. 👀 Preview auto-renders WeChat-formatted output on the right
3. 🎨 Pick a theme from the dropdown
4. 📋 Click **"📋 复制富文本"** (Copy Rich Text) button
5. 📱 Open WeChat Official Account editor → `Ctrl+V` / `Cmd+V` to paste

---

## 🎨 Themes / 主题

| Theme | Style | Best For |
|-------|-------|----------|
| 🤍 **Minimal** (简约) | Clean black & white | Daily articles, announcements |
| 💜 **Tech** (科技) | Purple gradient, modern | Tech blogs, product intros |
| 📖 **Literary** (文艺) | Serif fonts, classic feel | Essays, book reviews |
| 💙 **Business** (商务) | Blue tones, professional | Industry analysis, reports |

---

## 🛠 Tech Stack / 技术栈

| Technology | Purpose |
|-----------|---------|
| [marked.js](https://github.com/markedjs/marked) | Markdown parsing |
| [highlight.js](https://github.com/highlightjs/highlight.js) | Code syntax highlighting |
| Vanilla HTML/CSS/JS | UI & interactions |
| CDN | Zero-install dependency loading |
| GitHub Pages | Hosting & deployment |

---

## 📦 Project Structure / 项目结构

```
md2wechat/
├── index.html          # 🏠 Main app (single-file, all-in-one)
├── images/             # 🖼️ Article images & charts
├── .github/workflows/  # 🚀 GitHub Pages auto-deploy
├── *.md                # 📄 Sample WeChat articles
├── LICENSE             # 📄 MIT License
└── README.md           # 📖 This file
```

---

## 🤝 Contributing / 贡献指南

Contributions are welcome! Here's how you can help:

1. **Fork** this repository
2. **Create** a feature branch: `git checkout -b feature/amazing-feature`
3. **Commit** your changes: `git commit -m 'Add amazing feature'`
4. **Push** to the branch: `git push origin feature/amazing-feature`
5. **Open** a Pull Request

### Ideas for Contribution

- 🎨 New themes
- 🌍 i18n / English UI
- 📱 Mobile responsive improvements
- 🧩 Custom CSS editor
- 📤 Export to HTML/PDF

---

## ⭐ Star History

<p align="center">
  <a href="https://star-history.com/#Lifa-su/md2wechat&Date">
    <img src="https://api.star-history.com/svg?repos=Lifa-su/md2wechat&type=Date" alt="Star History Chart" width="600">
  </a>
</p>

---

## 📄 License

[MIT](LICENSE) © [Lifa-su](https://github.com/Lifa-su)

---

<p align="center">
  Made with ❤️ for WeChat content creators<br>
  <em>为微信公众号创作者而生</em>
</p>
