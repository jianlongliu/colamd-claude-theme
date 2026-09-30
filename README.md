# ColaMD Claude 主题

给 [ColaMD](https://colamd.app)（Electron + CodeMirror 的 Markdown 阅读/编辑器）用的浅色主题：暖米色纸面 + 陶土橙强调色，取色自 Anthropic Claude 的界面风格。

## 安装

```sh
mkdir -p ~/.colamd/themes
cp Claude.css ~/.colamd/themes/
```

然后**重启 ColaMD**——Theme 菜单里的自定义主题项是在 App 启动时扫描 `~/.colamd/themes/` 生成的，新文件不重启不会出现。重启后在 Theme 菜单里选 `Claude`。**菜单项显示的就是文件名（去掉 `.css`）**，大小写原样保留，所以想要显示成 `Claude` 就得把文件命名成 `Claude.css`。

改完文件内容不必重启：重新点一次 Theme 菜单里的 `Claude` 即可（点击时才读文件）。

命名相关的坑：ColaMD 用**文件名**做主题标识（`localStorage["colamd-theme"] = "custom:Claude.css"`），所以**重命名文件等于换了一个主题**——旧的选择记录会失效，启动时找不到对应文件，页面会退回无主题样式，重新在菜单里选一次即可。

## 版本适配：2.7 换了编辑器内核

ColaMD 2.7 起编辑器内核从 Milkdown 换成 CodeMirror，行类名的写法整个变了。本文件按 2.7+ 写；2.6 及更早的版本上，下面这几条按 `.cm-md-*` 写的规则不会命中任何元素：

| 元素 | 2.7+（CodeMirror） | 2.6−（Milkdown） |
|---|---|---|
| 一级标题 | `.cm-content .cm-md-atxheading1` | `.ProseMirror h1` |
| 代码块 | `.cm-content .cm-md-codeblock` | `.ProseMirror pre` |
| 行内代码 | `.cm-content .cm-md-inlinecode` | `.ProseMirror code` |

`:root` 里那套变量两代通用。

## 外观

底色用的是 Anthropic 设计 token 里的 `--color-gray-150`（`#f0eee6`，Claude 界面的奶油底），相邻表面按同一套官方灰阶错开一档，免得代码块糊进背景里。

| 元素 | 颜色 |
|---|---|
| 背景 | `#f0eee6`（`--color-gray-150`） |
| 正文 | `#33312e` |
| 次级文字 / 弱化文字 | `#6b675e` / `#98938a` |
| 链接 | `#c15f3c` |
| 引用块左边线 | `#d97757`（Anthropic `--color-clay`） |
| 行内代码底 / 字 | `rgba(200, 120, 80, 0.1)` / `#b8452a` |
| 代码块底 / 字 | `#e8e6dc`（`--color-gray-200`）/ `#4f4a40` |
| 引用块底 | `#faf9f5`（`--color-gray-050`） |
| 表格表头 / 描边 | `#e8e6dc` / `#dedcd1` |
| 高亮 | `#f7e7d5` |
| 一级标题 | `#c44b2b`（固定，覆盖正文色） |

`theme-*` 内置主题的 h1 只是继承正文色；这里显式给它压了一行红，以匹配 Claude 的标题观感。

### 代码块语法高亮

代码 token 的颜色不写在主题的 `:root` 里，而是由 `--code-*` 变量走：ColaMD 读 `--code-block-bg` 的明暗给 `body` 挂上 `code-palette-light`（浅底）或不挂（深底），两套调色板分别定义在各自的选择器下（见 `applyCodePalette()`）。内置的浅色那套是 GitHub Light 的冷色，压在暖米纸面上出戏，所以本主题整条覆盖：

| token | 颜色 |
|---|---|
| 关键词 | `#b8452a`（与 h1 同族的陶土红） |
| 字符串 | `#6b5b32` |
| 注释 | `#8a8478` |
| 数字 | `#9c5a1e` |
| 函数 | `#7d5a9e` |
| 类型 | `#8a5a2b` |
| 属性 | `#4f6b3a` |
| meta | `#6b6072` |

运算符、标点、括号刻意不给颜色（跟随代码块正文色），整块才不会花。注意这条覆盖挂在 `body.code-palette-light` 下：一旦把 `--code-block-bg` 改成深色，ColaMD 会切去深色那套，这段就不再生效，得换个选择器重写。

## 代码块字体：需要按环境改

文件末尾的两段 `@font-face` 指向本机字体的**绝对路径**，在你机器上多半不存在，请改成自己的或用不到的整段删掉：

```css
@font-face { font-family: "ColaMD Code";    src: url("file:///usr/share/fonts/TTF/JetBrainsMonoNerdFont-Regular.ttf"); }
@font-face { font-family: "ColaMD Code CN"; src: url("file:///usr/share/fonts/maple/MapleMonoNormalNL-NF-CN-Medium.ttf"); }
```

`ColaMD Code` 管西文与符号、`ColaMD Code CN` 管中文，按 fallback 顺序写在同一行 `font-family` 里。路径错了不会报错，只是静默回退到 `monospace`。

**为什么绕这一圈**：在部分 Linux 上，按族名写 `'JetBrains Mono'` 之类会被 fontconfig 规则劫持，实际渲染成另一个字体（可用 `fc-match "JetBrains Mono"` 核对——返回的不是 JetBrains 即中招）。`@font-face` + `file://` 直指字体文件可以完全绕开 fontconfig。

## 说明

- ColaMD 的自定义主题是**独占**的：它取代内置主题而不是叠加，所以本文件定义了整套 CSS 变量，而不是只覆盖几个点。想在内置主题上叠加单条规则（比如只要那行红标题）做不到。
- 自定义主题的内容是原样注入一个 `<style>`，因此除变量外可以写任意 CSS。
- 主题切换时 body 上的 class 会被重置，要长期生效的样式请注入 `<style>` 或写进主题文件，别依赖注入的 class。
- 选中自定义主题时，body 上**只有** `theme-custom` 这一个 class（内置的 `theme-*` 全被移除）。内置主题里那些 `body.theme-x .cm-content .cm-md-*` 的写法因此不会生效——行内代码的颜色就属于这一类，需要在主题文件里自己补。
