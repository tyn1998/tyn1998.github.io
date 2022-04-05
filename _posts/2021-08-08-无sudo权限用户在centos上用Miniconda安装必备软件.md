---
layout: post
---

<a name="xkqxx"></a>

## 基本信息

- 实验日期：2021 年 8 月 7 日
- 操作系统：CentOS 7，64 位
- 内核版本：3.10.0
- **sudo 权限：无**
- 干净的“家”目录：

![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628240731260-d1c3db7b-b37f-4601-a03a-e9d235fb4d8c.png#clientId=uc892d7de-d079-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=170&id=u72d27ef7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=170&originWidth=588&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26577&status=done&style=none&taskId=u4311c807-244f-4859-9eba-df832042f62&title=&width=588)

<a name="RNpyp"></a>

## 调研&尝试

这个[回答](https://stackoverflow.com/a/52567731)是起点，自此共了解了以下几种方案：

- rpm2cpio
- [Miniconda](https://stackoverflow.com/a/52561058)
- [user-yum.sh](https://gitlab.com/caroff/user-yum.sh)
- [JuNEST](https://github.com/fsquillace/junest)

除了 rpm2cpio 这个比较“人工”的方案，其他我都尝试了。But，最好用也是**唯一成功**的 Miniconda 方案竟然是我最后试的。

<a name="klPC2"></a>

## 原理简介

问：用 yum 安装软件为什么要 sudo？<br />答：yum 会把软件安装到系统目录下（e.g. /usr/bin），需要对这些目录的“写”权限。<br />问：Miniconda 是怎么工作的？<br />答：conda 把软件安装到用户家目录（用户具有完全读写权），然后用户再把这些软件的可执行程序目录添加到 PATH 中。

<a name="GhQyj"></a>

## 开始装机

Good luck！
<a name="DzrWe"></a>

### 安装 Miniconda

1. 脚本自动安装 Miniconda 到`~/conda`。

```bash
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh > Miniconda.sh
bash Miniconda.sh -b -p ~/conda
# -b is used to specify that this is done "in batch", so skip the EULA prompt
# -p lets you specify where you want conda installed
```

2. 添加`conda/bin`目录到 PATH。

```bash
# 将下面两行添加到你使用的shell的配置文件中（Fish用户使用set -x）
CONDABIN_D=$HOME/conda/bin
export PATH=$CONDABIN_D:$PATH
# Bash设置PATH以“:”分割，找可执行程序的顺序是从左到右
```

<a name="ZBVQ8"></a>

### 安装你要的软件

3. 去[anaconda 仓库](https://anaconda.org/search)搜索包。
   ![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628388284900-93cbc368-65ed-4a7e-9d38-5b243c50065c.png#clientId=ub8b97d14-4903-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=751&id=u4e54a75c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=751&originWidth=1381&originalType=binary&ratio=1&rotation=0&showTitle=false&size=119957&status=done&style=stroke&taskId=uedfd8d36-8380-4f1c-9c06-7a201a5e89f&title=&width=1381)

4. 进入包详情界面可以看到安装指令，复制过来回车。

```bash
# fish
conda install -c conda-forge fish
# ranger
conda install -c conda-forge ranger-fm
# tmux
conda install -c conda-forge tmux
# git
conda install -c conda-forge git
# vim
conda install -c conda-forge vim
# htop
conda install -c conda-forge htop
# node（with npm）
conda install -c conda-forge nodejs
# bat
conda install -c conda-forge bat
# fzf（conda装的fzf，vim认不出）
# conda install -c conda-forge fzf
# the_silver_searcher
conda install -c conda-forge the_silver_searcher
# lazygit
conda install -c conda-forge lazygit
# ncdu
conda install -c conda-forge ncdu
# trash-cli
conda install -c conda-forge trash-cli
# c++头文件
conda install -c conda-forge gxx_impl_linux-64
# clang++
conda install -c conda-forge clangxx
# clangd
conda install -c conda-forge clang-tools
# cmake
conda install -c conda-forge cmake
# ncurses
conda install -c conda-forge ncurses
# pkg-config
conda install -c conda-forge pkg-config
# flex
conda install -c conda-forge flex
# bison
conda install -c conda-forge bison
# bear
conda install -c bobbiewang bear
```

注意：如果安装的软件系统本身就有，可能需要重新登录才能生效。

快速：

```bash
conda install -y -c conda-forge fish ranger-fm git tmux vim htop nodejs bat lazygit ncdu the_silver_searcher trash-cli
```

<a name="yDjY3"></a>

## 可选操作

私用。

<a name="kBOPY"></a>

### 设置“默认”shell

如果你常用的 shell 不在`/etc/shells`中，那你没法用`chsh`来设置登录 shell。但你可以通过一些小技巧来达到类似的效果。

在`~/.bash_profile`最后加上：

```bash
export SHELL=/home/tyn/conda/bin/fish # shell可执行程序路径
exec $SHELL -l
```

> .bashrc 和.bash_profile 的区别是，后者是登录时 source 的，因此在 profile 里把 SHELL 设置为 fish，之后的默认 shell 都是 fish 了。

设置 fish 配色：

```bash
set -L
set -U fish_color_normal normal
set -U fish_color_command a1b56c
set -U fish_color_quote f7ca88
set -U fish_color_redirection d8d8d8
set -U fish_color_end ba8baf
set -U fish_color_error ab4642
set -U fish_color_param d8d8d8
set -U fish_color_comment f7ca88
set -U fish_color_match 7cafc2
set -U fish_color_selection c0c0c0
set -U fish_color_search_match ffff00
set -U fish_color_history_current normal
set -U fish_color_operator 7cafc2
set -U fish_color_escape 86c1b9
set -U fish_color_cwd 008000
set -U fish_color_cwd_root 800000
set -U fish_color_valid_path normal
set -U fish_color_autosuggestion 585858
set -U fish_color_user 00ff00
set -U fish_color_host normal
set -U fish_color_cancel normal
set -U fish_pager_color_completion normal
set -U fish_pager_color_description B3A06D yellow
set -U fish_pager_color_prefix normal --bold --underline
set -U fish_pager_color_progress brwhite --background=cyan
```

<a name="C2Mq9"></a>

### 打包备份用户家目录

创建一个`~/backup`目录用于存放备份文件，下面的命令可以将`~/backup`排除在打包范围外。

```bash
mkdir ~/backup
# 非fish用户
tar -zcvf $HOME/backup/home-backup-$(date +%Y-%m-%d-%H:%M).tar.gz --exclude=$HOME/backup $HOME
# fish用户
tar -zcvf $HOME/backup/home-backup-(date +%Y-%m-%d-%H:%M).tar.gz --exclude=$HOME/backup $HOME
```

![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628399784017-b86a5fef-d275-4855-adb5-cb6a6af171e9.png#clientId=u23f28807-8299-4&crop=0&crop=0&crop=1&crop=1&from=paste&id=ub3c0c38b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=607&originWidth=1601&originalType=binary&ratio=1&rotation=0&showTitle=false&size=65393&status=done&style=none&taskId=u22a90108-6872-4e23-8f2c-32943b31a79&title=)
<a name="xLBtn"></a>

### 系统剪切板

（暂无方案）
<a name="pTvwN"></a>

### 配合 vim 安装 fzf

conda 安装的 fzf 的目录结构对 vim 不适用，因此单独安装 fzf：

```bash
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

然后在.vimrc 中加入：`set runtimepath+=~/.fzf`。
<a name="anQpI"></a>

### 安装 pyenv 和 pyenv-virtualenv

1.  自动安装脚本：
```bash
curl https://pyenv.run | bash
```

2. 由于我的`.bash_profile`source 了`.bashrc`，根据官方文档，我需要在 source`.bashrc`之前加两句`export`，然后在进入 fish 前加另一句`eval`:
   ![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628324779385-dbd77fb3-d5f9-4e02-88bc-707c70c7577d.png#clientId=uc892d7de-d079-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=486&id=ubc5ff9a8&margin=%5Bobject%20Object%5D&name=image.png&originHeight=486&originWidth=628&originalType=binary&ratio=1&rotation=0&showTitle=false&size=44765&status=done&style=none&taskId=u5bad4741-779a-4507-96d9-3c54bf85d09&title=&width=628)

3. 然后在`config.fish`里：
   ![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628324863528-c7234e03-68a5-4cc9-8bd7-c213e68cf8aa.png#clientId=uc892d7de-d079-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=290&id=u941732da&margin=%5Bobject%20Object%5D&name=image.png&originHeight=290&originWidth=637&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28945&status=done&style=none&taskId=u1d56c5a3-9e88-451f-8fa7-425715ac3fa&title=&width=637)

4. 重新 ssh 登录生效。
（pyenv virtualenv xxx 会调用 mini conda 的 conda env create，[https://github.com/pyenv/pyenv-virtualenv#anaconda-and-miniconda](https://github.com/pyenv/pyenv-virtualenv#anaconda-and-miniconda)）

<a name="QYRA0"></a>

## 装完后截个图

图 1：机器的奢华被尽数体现，还做了用户目录的打包备份。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628353043469-7f8cb73c-516f-4fe2-9efd-2e7cf58b0443.png#clientId=ue2c6f418-eb96-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1078&id=u6687f9e7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1078&originWidth=1917&originalType=binary&ratio=1&rotation=0&showTitle=false&size=256708&status=done&style=none&taskId=u3bc8f0a6-2b6f-4236-8660-49cdd5be4be&title=&width=1917)<br />图 2：解决了一些在本机上根本没出现的问题，一致性把握不住了，为 dotfiles 新开了一个分支“jail-user”。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628353083174-d9f7e258-9e68-4178-97cc-fabb0802d542.png#clientId=ue2c6f418-eb96-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1080&id=u3077cd60&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=249172&status=done&style=none&taskId=ucefa28a7-8e41-474f-9acc-a6176baad11&title=&width=1920)<br />图 3： C++、python、javascript 的环境应该都没问题了（but，pyenv install 遇到网络问题）。<br />![image.png](https://cdn.nlark.com/yuque/0/2021/png/21625412/1628353118240-15ac387a-9d70-49f9-b22b-776cb04fb99c.png#clientId=ue2c6f418-eb96-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=1080&id=uf8983cc7&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1080&originWidth=1920&originalType=binary&ratio=1&rotation=0&showTitle=false&size=237283&status=done&style=none&taskId=ufc36cb63-5ef3-4aef-82b7-700ef6776d5&title=&width=1920)
