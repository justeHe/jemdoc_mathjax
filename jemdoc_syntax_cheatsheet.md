# Jemdoc 语法与 HTML 对应速查表 (详细版)

本文档基于 `jemdoc` 的 Python 脚本 (`jemdoc` version 0.7.3) 分析得出, 旨在详细说明 Jemdoc 的标记语法如何转换为标准的 HTML 元素, 并为每个语法点提供清晰的文字说明和示例.

---

## 1. 页面元信息与配置

Jemdoc 通过在文件顶部的特殊注释行 (`# jemdoc: ...`) 来进行页面级别的配置.

### 1.1. 页面标题 (`title`)
- **说明**: 设置在浏览器标签页中显示的标题.
- **Jemdoc 语法**: `# jemdoc: title{Your Page Title}`
- **HTML 对应**: 
  ```html
  <head>
    ...
    <title>Your Page Title</title>
    ...
  </head>
  ```

### 1.2. 导航菜单 (`menu`)
- **说明**: 创建一个侧边栏导航菜单. Jemdoc 会自动高亮指向当前页面的链接.
- **Jemdoc 语法**: `# jemdoc: menu{menu_file.jemdoc}{current_page.html}`
- **HTML 对应**: 生成一个 `<div id="layout-menu">...</div>`, 内部包含一系列的 `div.menu-item` 和 `div.menu-category`. 当前页面的链接会带有 `class="current"`.
  ```html
  <!-- 假设 menu_file.jemdoc 包含 "Home [index.html]" -->
  <div class="menu-item"><a href="index.html" class="current">Home</a></div>
  ```

### 1.3. 样式与脚本 (`css`, `js`)
- **说明**: 可以在默认样式基础上添加自定义的 CSS 和 JavaScript 文件.
- **Jemdoc 语法**:
  ```jemdoc
  # jemdoc: addcss{custom.css}
  # jemdoc: addjs{custom.js}
  # jemdoc: nodefaultcss  // 此项会禁止加载默认的 jemdoc.css
  ```
- **HTML 对应**:
  ```html
  <head>
    ...
    <link rel="stylesheet" href="custom.css" type="text/css" />
    <script src="custom.js" type="text/javascript"></script>
    ...
  </head>
  ```

---

## 2. 块级元素 (Block-Level Elements)

### 2.1. 标题 (Headings)
- **说明**: 使用一到多个 `=` 开头来定义不同级别的标题.
- **Jemdoc 语法**:
  ```jemdoc
  = 标题 1 (H1)
  == 标题 2 (H2)
  === 标题 3 (H3)
  ```
- **HTML 对应**:
  ```html
  <h1>标题 1 (H1)</h1>
  <h2>标题 2 (H2)</h2>
  <h3>标题 3 (H3)</h3>
  ```

### 2.2. 段落 (Paragraphs)
- **说明**: 任何连续的文本行都会被视为一个段落, 并被包裹在 `<p>` 标签中. 段落之间用一个或多个空行分隔.
- **Jemdoc 语法**:
  ```jemdoc
  这是第一个段落.
  它可以在多行书写.

  这是第二个段落.
  ```
- **HTML 对应**:
  ```html
  <p>这是第一个段落. 它可以在多行书写.</p>
  <p>这是第二个段落.</p>
  ```

### 2.3. 列表 (Lists)
- **无序列表**: 使用 `-` 创建, 增加前导 `-` 的数量可以创建嵌套列表.
  - **Jemdoc**:
    ```jemdoc
    - 列表项 A
    - 列表项 B
      -- 嵌套项 B.1
    ```
  - **HTML**:
    ```html
    <ul>
      <li><p>列表项 A</p></li>
      <li><p>列表项 B</p>
        <ul><li><p>嵌套项 B.1</p></li></ul>
      </li>
    </ul>
    ```
- **有序列表**: 使用 `.` 创建, 同样支持嵌套.
  - **Jemdoc**:
    ```jemdoc
    . 第一项
    . 第二项
    ```
  - **HTML**:
    ```html
    <ol>
      <li><p>第一项</p></li>
      <li><p>第二项</p></li>
    </ol>
    ```
- **定义列表**: 使用 `:{术语} 定义` 的格式.
  - **Jemdoc**: `:{Jemdoc} 一种轻量级标记语言.`
  - **HTML**:
    ```html
    <dl>
      <dt>Jemdoc</dt>
      <dd><p>一种轻量级标记语言.</p></dd>
    </dl>
    ```

### 2.4. 区块 (Blocks)
- **说明**: 使用 `~~~` 包裹来创建特殊的格式化区块, 如信息框和代码块.
- **信息区块 (Info Block)**: 用于高亮显示提示信息.
  - **Jemdoc**:
    ```jemdoc
    ~~~ {可选的标题}
    这里是区块的内容.
    ~~~
    ```
  - **HTML**:
    ```html
    <div class="infoblock"><div class="blocktitle">可选的标题</div><div class="blockcontent"><p>这里是区块的内容.</p></div></div>
    ```
- **代码区块 (Code Block)**: 用于展示代码, 支持语法高亮.
  - **Jemdoc**: `~~~ {标题}{语言}`. 支持的语言包括 `python`, `c`, `cpp`, `java`, `matlab`, `sh` 等.
    ```jemdoc
    ~~~ {Python 示例}{python}
    def greet(name):
        print(f"Hello, {name}!")
    ~~~
    ```
  - **HTML**:
    ```html
    <div class="codeblock"><div class="blocktitle">Python 示例</div><div class="blockcontent"><pre>
    <span class="statement">def</span> greet(name):
        <span class="statement">print</span>(<span class="string">f"Hello, {name}!"</span>)
    </pre></div></div>
    ```

---

## 3. 行内元素 (Inline-Level Elements)

### 3.1. 文本格式化
- **粗体**: `*文本*` &rarr; `<b>文本</b>`
- **斜体**: `/文本/` &rarr; `<i>文本</i>`
- **下划线**: `_文本_` &rarr; `<u>文本</u>`
- **等宽字体**: `+文本+` &rarr; `<tt>文本</tt>`
- **引号**: `"文本"` &rarr; `&ldquo;文本&rdquo;`

### 3.2. 链接 (Links)
- **说明**: 使用 `[URL 链接文本]` 的格式.
- **外部链接**: 默认在新标签页打开.
  - **Jemdoc**: `[http://jemdoc.jaboc.net Jemdoc 官网]`
  - **HTML**: `<a href="http://jemdoc.jaboc.net" target="blank">Jemdoc 官网</a>`
- **内部链接**: 在 URL 前加 `/` 会在当前标签页打开.
  - **Jemdoc**: `[/research.html 我的研究]`
  - **HTML**: `<a href="research.html">我的研究</a>`
- **邮件链接**: 自动识别邮件地址.
  - **Jemdoc**: `[name@example.com]`
  - **HTML**: `<a href="mailto:name@example.com">name@example.com</a>`

### 3.3. 图像 (Images)
- **说明**: 语法为 `[img{width}{height}{alt_text} image_url]`. 花括号内的参数是可选的.
- **Jemdoc**: `[img{100px}{}{这是一个Logo} images/logo.png]`
- **HTML**: `<img src="images/logo.png" width="100px" alt="这是一个Logo" />`

### 3.4. 数学公式 (MathJax)
- **说明**: 该版本的 Jemdoc 已被修改以支持 MathJax.
- **行内公式**: 使用 `$...$` 包裹.
  - **Jemdoc**: `爱因斯坦的质能方程是 $E=mc^2$.`
  - **HTML (MathJax)**: `爱因斯坦的质能方程是 \(E=mc^2\).`
- **块级公式**: 使用 `\(...\)` 包裹.
  - **Jemdoc**: `\( \sum_{i=1}^n i = \frac{n(n+1)}{2} \)`
  - **HTML (MathJax)**: `\[ \sum_{i=1}^n i = \frac{n(n+1)}{2} \]`

### 3.5. 特殊字符与符号
- `---` (em-dash) &rarr; `&#8201;&mdash;&#8201;`
- `--` (en-dash) &rarr; `&ndash;`
- `...` (ellipsis) &rarr; `&hellip;`
- `~` (non-breaking space) &rarr; `&nbsp;`
- `\R` (Registered trademark) &rarr; `&reg;`
- `\C` (Copyright) &rarr; `&copy;`
- `\M` (Middle dot) &rarr; `&middot;`
- `\n` (Line break) &rarr; `<br />`

### 3.6. 原始 HTML
- **说明**: 如果需要插入 Jemdoc 不支持的 HTML, 可以使用 `{{...}}` 将其包裹起来.
- **Jemdoc**: `{{<div style="color:red;">这是红色的原始HTML.</div>}}`
- **HTML**: `<div style="color:red;">这是红色的原始HTML.</div>`

---

## 4. 表格 (Tables)

- **说明**: Jemdoc 支持通过特定的区块语法创建表格. 使用 `|` 分隔单元格 (columns), 使用 `||` 结束一行 (rows).
- **Jemdoc 语法**:
  - 表格区块以 `~~~{}{table}{optional-id}` 开始.
  - 第一个花括号 `{}` 用于标题, 但在表格中通常被忽略.
  - 第二个花括号 `{table}`是必需的关键字.
  - 第三个花括号 `{optional-id}` 可以为表格设置一个 CSS `id`.
  ```jemdoc
  ~~~{}{table}{my-table}
  表头 1 | 表头 2 | 表头 3 ||
  行 1, 列 1 | 行 1, 列 2 | 行 1, 列 3 ||
  行 2, 列 1 | 行 2, 列 2 | 行 2, 列 3
  ~~~
  ```
- **HTML 对应**:
  ```html
  <table id="my-table">
    <tr class="r1">
      <td class="c1">表头 1 </td>
      <td class="c2"> 表头 2 </td>
      <td class="c3"> 表头 3 </td>
    </tr>
    <tr class="r2">
      <td class="c1">行 1, 列 1 </td>
      <td class="c2"> 行 1, 列 2 </td>
      <td class="c3"> 行 1, 列 3 </td>
    </tr>
    <tr class="r3">
      <td class="c1">行 2, 列 1 </td>
      <td class="c2"> 行 2, 列 2 </td>
      <td class="c3"> 行 2, 列 3</td>
    </tr>
  </table>
  ```

---

## 5. 高级功能 (Advanced Features)

### 5.1. 高级区块 (Advanced Blocks)

- **图文混排 (`img_left`)**: 创建一个左侧是图片, 右侧是文字描述的区块.
  - **Jemdoc 语法**: `~~~{}{img_left}{image_url}{alt_text}{width}{height}{link_url}`
    - `{}`: 标题 (忽略)
    - `{img_left}`: 关键字
    - `{image_url}`: 图片路径
    - `{alt_text}`: 图片的替代文本
    - `{width}`: 图片宽度 (e.g., `150px`)
    - `{height}`: 图片高度 (可选)
    - `{link_url}`: 图片点击链接 (可选)
  - **Jemdoc 示例**:
    ```jemdoc
    ~~~{}{img_left}{images/photo.jpg}{My Photo}{120px}{}{http://my-profile.com}
    这是我的个人简介.
    我是一名热爱编程的开发者.
    ~~~
    ```
  - **HTML 对应**:
    ```html
    <table class="imgtable">
      <tr>
        <td>
          <a href="http://my-profile.com"><img src="images/photo.jpg" alt="My Photo" width="120px" /></a>&nbsp;
        </td>
        <td align="left"><p>这是我的个人简介.<br />我是一名热爱编程的开发者.</p></td>
      </tr>
    </table>
    ```

### 5.2. 文件包含 (File Inclusion)

- **说明**: 将其他文件的内容插入到当前文档中, 便于模块化管理.
- **`include{...}`**: 包含另一个 `.jemdoc` 文件, 并对其内容进行解析.
  - **Jemdoc**: `include{shared/header.jemdoc}`
  - **行为**: `shared/header.jemdoc` 文件的内容会被解析并插入到当前位置.
- **`includeraw{...}`**: 直接将文件内容作为原始文本插入, 不进行任何解析.
  - **Jemdoc**: `includeraw{data.txt}`
  - **行为**: `data.txt` 文件的内容会被原封不动地插入.

### 5.3. 页脚定制 (Footer Customization)

- **说明**: 通过页面元信息可以控制页脚的显示内容.
- **Jemdoc 语法**:
  ```jemdoc
  # jemdoc: nofooter       // 完全不显示页脚
  # jemdoc: nodate          // 不显示页面生成日期
  # jemdoc: notime          // 不显示页面生成时间 (只显示日期)
  # jemdoc: showsourcelink  // 显示指向 .jemdoc 源文件的链接
  ```

### 5.4. 环境变量 (Environment Variables)

- **说明**: 可以在文档中引用操作系统的环境变量.
- **Jemdoc 语法**: `!$VAR_NAME$!`
  - `VAR_NAME` 是环境变量的名称.
- **Jemdoc 示例**: `我的主目录是: !$HOME$!`
- **HTML 对应**: `我的主目录是: /Users/your_username!` (假设 `HOME` 环境变量的值是 `/Users/your_username`)