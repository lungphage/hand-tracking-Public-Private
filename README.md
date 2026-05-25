# 手部追踪绘画 & 选框

基于 MediaPipe Hands 的指尖绘画和选框工具。

## 功能

- ✏️ **写字模式** — 用食指+中指手势在空中书写
- ⬚ **选框模式** — 双手食指拖拽绘制矩形选框
- 🧹 **橡皮擦** — 擦除已有笔迹
- ↩️ **撤销** — 撤销上一步操作
- 🗑️ **清空** — 清空画布
- 🎨 **颜色选择** — 自定义画笔颜色
- 📏 **粗细调节** — 调整画笔粗细

## 手势控制

| 手势 | 功能 |
|------|------|
| ✌️ 食指+中指 | 绘制/写字 |
| 👆 仅食指 | 移动光标（不绘制） |
| ✊ 握拳 | 停止绘制 |
| 🖐️ 五指张开2秒 | 清空画布 |

## 使用方法

### 本地运行

由于浏览器安全限制，需要通过本地服务器运行：

```bash
# 使用 Python
cd hand-tracking-project
python -m http.server 8080

# 然后访问 http://localhost:8080
```

### 文件说明

**本地离线版（无需联网）：**
- `index.html` — 基础版本，简单的绘画功能
- `full-version.html` — 完整版本，包含所有功能
- `mediapipe/` — MediaPipe 模型和库文件

**CDN 在线版（需要联网）：**
- `index-cdn.html` — 基础版本，从 CDN 加载模型
- `full-version-cdn.html` — 完整版本，从 CDN 加载模型

## 技术栈

- MediaPipe Hands — 手部关键点检测
- Canvas 2D — 绘图
- getUserMedia — 摄像头访问

## 浏览器兼容性

- Chrome 90+
- Edge 90+
- Firefox 90+
- Safari 14+

## 注意事项

1. 需要 HTTPS 或 localhost 环境才能访问摄像头
2. 光线要充足，手要正对摄像头
3. 建议距离摄像头 30-50cm
4. 模型文件已打包在 `mediapipe/` 目录，可离线使用
