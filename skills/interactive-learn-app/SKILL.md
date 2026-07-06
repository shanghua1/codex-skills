---
name: interactive-learn-app
description: 构建带 AI 辅助的交互式学习网页应用。单 HTML 文件，支持任意题材（编程、剪辑、建模、英语等）。包含课程导航、随堂测验、交互练习、AI 出题/讲解/问答、进度持久化、Skill 管理。当用户想创建学习网站、教程页面、交互式课程、AI 辅助教学系统时使用。
---

# Interactive Learn App

从模板生成单文件交互式学习应用，自带 AI 问答、测验、练习、进度保存。

## 快速启动

用户说"帮我做一个 XX 的学习应用"时，按以下步骤：

1. 复制模板：`assets/template.html`
2. 修改 `<title>`
3. 填充 `CONTENT_CONFIG`（见下文）
4. 填充 `AI_CONFIG`
5. 编写 `COURSE[]` 课程数据
6. 用浏览器打开测试

## 模板结构

单 HTML 文件，三大可配置区域 + 固定引擎：

```
┌─────────────────────────────────────────────────┐
│  <style>  固定 CSS（不要动）                      │
│  ─────────────────────────────────────────────── │
│  <body>  固定 HTML 结构（不要动）                  │
│  ─────────────────────────────────────────────── │
│  <script>                                        │
│    1. CONTENT_CONFIG  ← 改：题材基本信息           │
│    2. AI_CONFIG       ← 改：AI 角色和提示词        │
│    3. COURSE[]        ← 改：全部课程内容            │
│    ───────────────────────────────────────────── │
│    固定 JS 引擎（不要动）                          │
│  </script>                                       │
└─────────────────────────────────────────────────┘
```

## CONTENT_CONFIG

```javascript
var CONTENT_CONFIG = {
    topic: '课程标题',           // 显示在侧边栏顶部
    subtitle: '交互式学习系统',  // 副标题
    codeLang: 'java',           // 语法高亮语言：java/python/javascript/text
    practiceMode: 'code',       // 'code'=写代码  'text'=文字回答
    codefixEnabled: true,       // 是否启用改错练习
    analogyBox: { title: 'MC 类比', icon: '🎮' },  // 类比框标题和图标
    pathHighlightRegex: /正则/g,  // 代码中文件路径高亮
    syntaxHighlight: {
        keywords: '...'.split(' '),  // 语言关键字
        types: '...'.split(' '),     // 类型名
        singleLineComment: '//',
        multiLineComment: ['/*', '*/']
    }
};
```

## AI_CONFIG

```javascript
var AI_CONFIG = {
    qaSystemPrompt: '你是 XX 专家...\n回答按以下格式（Markdown）：\n## 📋 是什么\n简洁解释。\n## 🔧 怎么用\n代码示例 + 文件路径。\n## ⚠️ 注意事项\n常见坑。\n## 🎮 实际场景\n具体应用。',
    quizGenPrompt: '根据课程内容出 3 道选择题，不要和已有题目重复。严格返回 JSON：[{"q":"题目","opts":["A","B","C","D"],"a":0,"explain":"解释"}]。选项不要加 A./B./C./D. 前缀。',
    exerciseGenPrompt: '出一道编程练习题。严格返回 JSON：{"prompt":"题目","hint":"提示","answer":"正确代码","review":"带注释的参考代码"}',
    explainPrompt: '用简洁中文讲解以下知识点，配合实际例子，300 字以内。',
    compressPrompt: '压缩对话为摘要：主题 + 知识点 + 重要代码 + 遗留问题',
    quickQuestions: [
        { label: '快捷问题1', text: '问题内容' },
        // 4-5 个
    ]
};
```

## COURSE[] 课程数据

每课结构：

```javascript
{
    id: 1,
    title: '课程标题',
    phase: 1,
    phaseName: '阶段名称',
    content: '<h2>💡 概念</h2><p>解释...</p><div class="mc"><h3>🎮 类比</h3>...</div><pre><code>代码</code></pre>',
    quizzes: [
        { q: '题目（支持 Markdown）', opts: ['选项A','选项B','选项C','选项D'], a: 1, explain: '解释' }
        // 6 道
    ],
    practice: [
        { prompt: '题目', hint: '提示', answer: '正确代码|备选答案', review: '带注释的参考代码' }
        // 2-4 个
    ],
    codefix: [
        { prompt: '题目', buggy: '错误代码', fixed: '正确代码', hint: '提示', review: '解析' }
        // 2-4 个，可选
    ]
}
```

### content HTML 格式

- 知识点用 `<h2>`，带 emoji 前缀
- 类比框用 `<div class="mc">`
- 代码块用 `<pre><code>`，**逐行中文注释**
- 表格用内联样式的 `<table>`
- 不要用外部 Markdown 库，引擎内置 `mdInline()` 处理行内格式

### 测验规则

- 每课 6 道选择题
- 选项不加 `A.` `B.` 前缀（引擎自动添加）
- `explain` 可选（答错时显示）
- 不交叉出题，每题对应当前知识点

### 练习规则

- `answer` 用 `|` 分隔多个正确答案
- `review` 是带注释的参考代码
- `hint` 简短一行

### 改错练习规则

- `buggy` 必须有明显错误
- `fixed` 是修正后的完整代码
- `hint` 指出方向，不说答案

## 踩坑清单（必读）

### 1. 不要用 `file:///` 打开

本地 `file://` 会阻止外部 CDN 加载（字体、图标等）。必须用 HTTP 服务打开：
```bash
# 方法一：Python
python -m http.server 8080
# 方法二：VS Code Live Server
# 方法三：直接部署到 GitHub Pages
```

### 2. CDN 在国内可能失效

`jsdelivr`、`cdnjs` 在国内加载不稳定。当前模板已内联所有 CSS/JS，不依赖外部 CDN。
如果需要 Mermaid 图表，用 `mermaid.ink` 在线渲染服务，不要加载 mermaid.min.js：
```
https://mermaid.ink/img/<base64 编码的 Mermaid 代码>
```

### 3. MiMo 模型的 reasoning_content

MiMo 模型会先输出 `reasoning_content`（思考过程），再输出 `content`。如果设了 `max_tokens`，思考过程可能耗尽所有 token，导致 `content` 为空。

**解决方案**：不设 `max_tokens`，让用户自行控制。

### 4. 语法高亮必须跳过隐藏元素

如果 `highlight.js` 在练习编辑器之前运行，会修改隐藏的 `answer` 内容，导致答案比对失败。

**解决方案**：练习的参考答案硬编码在 JS 中，不从 DOM 解析。

### 5. 大括号平衡是致命问题

对 `<script>` 区域做增量编辑时，极易导致 `{}` 不匹配，整个 JS 报错且难以定位。

**解决方案**：
- 始终以 `assets/template.html` 为基础
- 编辑后检查大括号平衡
- 如果出问题，回退到模板重新开始，不要试图逐行修复

### 6. 事件委托优于内联 onclick

内联 `onclick` 在动态生成的 HTML 中可能失效。用事件委托：
```javascript
document.addEventListener('click', function(e) {
    if (e.target.classList.contains('quiz-option')) { /* ... */ }
});
```

### 7. localStorage key 要唯一

不同题材的应用必须用不同的 key，避免进度数据冲突：
```javascript
var PROGRESS_KEY = '剪辑学习_progress';  // 不要用默认的
var CFG_KEY = '剪辑学习_cfg';
```

### 8. API 协议自动检测

模板支持 OpenAI 和 Anthropic 两种协议，通过 URL 自动判断：
- URL 含 `anthropic` → Anthropic 协议
- 其他 → OpenAI 协议

### 9. 代码块用 `white-space: pre` 不用 `pre-wrap`

`pre-wrap` 会折行，破坏树形目录结构。代码块统一用 `pre`。

### 10. Mermaid 单行问题

AI 生成的 Mermaid 代码经常是单行，本地渲染会失败。用 `mermaid.ink` 服务可以自动处理。

## 示例配置

### 剪辑学习

```javascript
var CONTENT_CONFIG = {
    topic: '视频剪辑', subtitle: '从零开始学剪辑',
    codeLang: 'javascript', practiceMode: 'text', codefixEnabled: false,
    analogyBox: { title: '生活类比', icon: '🎬' },
    pathHighlightRegex: /([A-Z]:\\[^\s<]+|\.mp4|\.prproj|\.aep)/g,
    syntaxHighlight: { keywords: 'const let var function return if else for while'.split(' '), types: 'Array Object String Number'.split(' '), singleLineComment: '//', multiLineComment: ['/*','*/'] }
};
```

### 3D 建模

```javascript
var CONTENT_CONFIG = {
    topic: '3D 建模', subtitle: 'Blender 建模入门',
    codeLang: 'python', practiceMode: 'code', codefixEnabled: true,
    analogyBox: { title: '手工类比', icon: '🎨' },
    pathHighlightRegex: /([A-Z]:\\[^\s<]+|\.blend|\.obj|\.fbx)/g,
    syntaxHighlight: { keywords: 'def class if elif else for while return import from as with try except finally raise yield lambda and or not in is None True False'.split(' '), types: 'int float str list dict tuple set bool bytes type object'.split(' '), singleLineComment: '#', multiLineComment: ['"""','"""'] }
};
```

### 英语学习

```javascript
var CONTENT_CONFIG = {
    topic: '实用英语', subtitle: '场景化英语学习',
    codeLang: 'text', practiceMode: 'text', codefixEnabled: false,
    analogyBox: { title: '中文类比', icon: '📚' },
    pathHighlightRegex: /(不可匹配)/g,
    syntaxHighlight: null  // 禁用语法高亮
};
```

## 资源

- `assets/template.html` — 完整模板文件（907 行），复制后直接修改三大配置区域即可
- `references/content-rules.md` — 课程内容编写详细规范
