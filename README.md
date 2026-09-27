# ColaMD Claude 主题

给 [ColaMD](https://colamd.app)（Electron + Milkdown 的 Markdown 阅读/编辑器）用的浅色主题：暖米色纸面 + 陶土橙强调色，取色自 Anthropic Claude 的界面风格。

## 安装

```sh
mkdir -p ~/.colamd/themes
cp claude.css ~/.colamd/themes/
```

然后**重启 ColaMD**——Theme 菜单里的自定义主题项是在 App 启动时扫描 `~/.colamd/themes/` 生成的，新文件不重启不会出现。重启后在 Theme 菜单里选 `claude`。

改完文件内容不必重启：重新点一次 Theme 菜单里的 `claude` 即可（点击时才读文件）。

## 外观

| 元素 | 颜色 |
|---|---|
| 背景 | `#f5f4ef` |
| 正文 | `#33312e` |
| 次级文字 / 弱化文字 | `#6b675e` / `#98938a` |
| 链接 | `#c15f3c` |
| 引用块左边线 | `#d97757` |
| 行内代码底 | `rgba(200, 120, 80, 0.1)` |
| 代码块底 / 字 | `#efece3` / `#4f4a40` |
| 高亮 | `#f7e7d5` |
| 一级标题 | `#c44b2b`（固定，覆盖正文色） |

`theme-*` 内置主题的 h1 只是继承正文色；这里显式给它压了一行红，以匹配 Claude 的标题观感。

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
