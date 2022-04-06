---
layout: slide
tags: [slides]
categories: [Tooling]
---

<section data-markdown>
<textarea data-template>
### keyboard mapping
   - brew install karabiner
      - 大小写 -> 左control
      - 右command+hjkl -> 左下上右
   - 安装百度输入法，用shift切换中/英
   - 标准打字法
---
### window management
   - app window shortcut
      - 微信：ctrl+command+W
      - iterm2：ctrl+command+T
   - virtual desktop(or called workspace) switch
      - ctrl+左右上下（用映射后的）
      - 触发角（用鼠标的时候方便）
   - 外接显示器、MacOS禁止自动换桌面顺序|只能移动非active桌面
---
### chrome
   - 用多窗口而不是数不清的标签（optional）
   - vimium插件解放鼠标
---
### terminal, shell, PATH

<section>

#### terminal
  - terminal枚举：MacOS的终端、iterm2、vscode里的小终端？
  - 终端下的快捷键：ctrl+A E U N P L
  - 终端样式：配色、字体
</section>

<section>

#### shell
 - shell枚举：bash、zsh、fish等等
 - PATH：是shell的环境变量之一（env命令查看所有环境变量）
    - 可执行程序的位置（用ranger去/bin等目录逛一下）
    - PATH用来告诉shell忘哪里找可执行程序
    - PATH的查找顺序（版本管理、mini conda）
 - shell配置文件（/etc/profile、~/profile，继承和覆盖）

</section>

<section>

 - iTerm2 + zsh + oh my zsh!（zsh的brew） + powerlevel10k
 - PS：pip、npm也可以装全局的包（-g）

</section>
 
---

### man, help, GitHub issues（授人以渔）

- man 中简单操作：j, k, d, u, /, n, N
- xxx --help | less 然后 j, k ,d, u, /, n, N
- 前往程序对应的 GitHub issues 页面看看有没有相关内容
- 程序中的 helper：vim :h, tmux prefix+?, ranger ?

---

### ranger

- 移动、剪切复制粘贴、多 tab、改名、预览

---

### tmux

- session、window、pane
- session 恢复（resurrect）
- tmux in tmux (nested)

---

### lazygit (optional)

---

### vim (optional)

---

### config files and dotfiles

- 配置文件无处不在（大多是以.开头的隐藏文件）
- 一次配置，终身受用，托管于 GitHub，更易管理 --> dotfiles
- 核心原理：软连接 + shell 脚本
</textarea>
</section>
