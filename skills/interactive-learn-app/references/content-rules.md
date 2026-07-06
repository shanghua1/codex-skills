# 课程内容编写规范

## HTML 格式

### 知识点标题

```html
<h2>💡 什么是 XXX</h2>
<p>简洁解释，2-3 句话。</p>
```

### 类比框

```html
<div class="mc">
    <h3>🎮 MC 类比</h3>
    <ul>
        <li>类比1：xxx 就像 yyy</li>
        <li>类比2：xxx 就像 yyy</li>
    </ul>
</div>
```

### 代码块

每行必须有中文注释，标注是否必需、单词含义：

```html
<pre><code class="language-java">
// 声明一个整数变量 health    ← 解释这行的作用
int health = 20;              // int = 整数类型, health = 变量名

// final 表示常量，不可修改    ← 解释关键字
final int MAX_STACK = 64;     // MAX_STACK = 常量名（全大写）
</code></pre>
```

### 表格

使用内联样式，不依赖外部 CSS：

```html
<table style="width:100%;border-collapse:collapse;margin:1rem 0;font-size:0.94rem">
    <thead><tr>
        <th style="padding:0.5rem;border:1px solid var(--border);background:rgba(91,138,114,0.06);color:var(--accent)">列1</th>
        <th style="padding:0.5rem;border:1px solid var(--border);background:rgba(91,138,114,0.06);color:var(--accent)">列2</th>
    </tr></thead>
    <tbody><tr>
        <td style="padding:0.5rem;border:1px solid var(--border)">内容</td>
        <td style="padding:0.5rem;border:1px solid var(--border)">内容</td>
    </tr></tbody>
</table>
```

### 行内格式

引擎内置 `mdInline()` 支持：
- `**加粗**` → `<strong>`
- `` `代码` `` → `<code>`
- 不支持标题、列表等块级 Markdown

## 测验编写

### 标准格式

```javascript
{
    q: '玩家的 X 坐标（123.456）应该用什么类型？',
    opts: ['int', 'double', 'boolean', 'String'],
    a: 1,  // 索引从 0 开始
    explain: '坐标是小数，用 double 类型'
}
```

### 规则

- 每课 **恰好 6 道**
- 选项不加 `A.` `B.` 前缀
- 每题只考一个知识点
- 不出超纲题（当前课程未讲的内容）
- `explain` 用简洁语言解释为什么选这个

## 练习编写

### 标准格式

```javascript
{
    prompt: '声明一个 int 类型的变量 health，值为 20',
    hint: '格式：类型 变量名 = 值;',
    answer: 'int health = 20;',  // 多个答案用 | 分隔
    review: '// 声明整数变量\nint health = 20;  // int = 整数类型'
}
```

### 规则

- `answer` 的比对是**归一化后精确匹配**（去空行、trim）
- 如果有多种写法，用 `|` 分隔：`'int x = 1;|int x=1;'`
- `review` 是带注释的参考代码，答对后显示
- `hint` 简短一行，指出方向

## 改错练习编写

### 标准格式

```javascript
{
    prompt: '以下代码试图声明一个常量，找出错误',
    buggy: 'int MAX_HEALTH = 20;\nMAX_HEALTH = 30;',
    fixed: 'final int MAX_HEALTH = 20;\n// 常量不可重新赋值',
    hint: '想想怎么让变量不可修改',
    review: 'final 关键字表示常量，赋值后不可修改'
}
```

### 规则

- `buggy` 必须有**且仅有一个**明显错误
- 错误类型：语法错误、拼写错误、逻辑错误
- `hint` 指出方向，不直接说答案
- `review` 可选，解释正确的做法

## 课程内容节奏

每课建议结构：
1. **概念引入**（h2 + 类比框）— 1-2 个知识点
2. **代码示例**（pre/code）— 带逐行注释
3. **表格对比**（可选）— 多个类型/概念对比
4. **随堂测验**（6 道）— 每学完一个知识点就出题
5. **交互练习**（2-4 个）— 动手写代码
6. **改错练习**（2-4 个，可选）— 找 bug

## 内容质量要求

课程内容必须**专业、丰富、饱满**，不能只有干巴巴的概念定义：

- **每个知识点都要有完整的代码示例**，不能只写文字描述
- **代码示例要带实际应用场景**，比如"在 MC 模组中，这通常用于 XXX"
- **类比要具体**，不能只说"就像 XX"，要展开解释为什么像
- **表格对比要详细**，列出类型、用途、代码示例、注意事项
- **测验题目要覆盖细节**，不能只考表面概念，要考易错点和实际用法
- **练习和改错要有代表性**，选最常见的错误和最实用的写法

## 内容长度建议

- 单课 content 控制在 **200-400 行 HTML**
- 不要一次塞太多知识点，2-3 个为佳
- 代码示例不超过 15 行，太长会失去注意力
- 测验紧跟知识点，不要等到课末
