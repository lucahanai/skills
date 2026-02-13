# Skills

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> 模块化技能库 - 每个目录一个可复用的技能组件

## 📚 简介

这是一个技能库项目，**每个目录代表一个独立的技能（Skill）**。每个技能都是自包含的模块，包含实现代码、文档和测试，可直接复制使用。

## 🗂️ 技能列表

| 技能 | 描述 | 状态 |
|------|------|------|
| [mobile-use](./mobile-use) | 通过 USB 控制 Android 手机 | ✅ |

---

## 📱 mobile-use

通过 USB 连接控制 Android 手机的自动化技能。

### 使用前提

1. **使用 USB 连接到 Android 手机**
   - 用数据线将手机连接到电脑
   - 确保手机开启 USB 调试模式

2. **下载 droidrun-portal APK**
   - 访问 [droidrun-portal releases](https://github.com/droidrun/droidrun-portal/releases)
   - 下载最新版本的 APK 文件

3. **安装 APK 到 Android 手机**
   ```bash
   adb install droidrun-portal-x.x.x.apk
   ```

4. **开启 Accessibility Service**
   - 打开 Droidrun Portal App
   - 点击"打开无障碍设置"
   - 进入 辅助功能 / Accessibility
   - 开启服务

---

## 🚀 快速开始

### 克隆仓库

```bash
git clone git@github.com:lucahanai/skills.git
cd skills
```


## 📄 许可证

[MIT](LICENSE) © lucahanai

---

*构建你的技能库，一块砖一块砖* 🧱
