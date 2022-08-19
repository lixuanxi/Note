# mac日记

## 1. 激活设置

**三指拖移**

**访达设置**

**触发角**

**程序坞**



## 2. 环境软件

**clash**

**Homebrew**

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

如果安装不成功，从`clash`处复制代理在终端输入在执行此命令



**iterm2 + oh my zsh**

oh my zsh插件：

`zsh-syntax-highlighting`

```bash
# 克隆项目
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
# 在 ~/.zshrc 中配置
plugins=(其他的插件 zsh-syntax-highlighting)
```

`zsh-autosuggestions`

```bash
# 克隆项目
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# 在 ~/.zshrc 中配置
plugins=(其他的插件 zsh-autosuggestions)

# ZSH_HIGHLIGHT_STYLES[path]=none
# ZSH_HIGHLIGHT_STYLES[path_prefix]=none	// 取消下划线
```







## 3. 快捷键

**文件移动：**

复制：`option + 拖动`

移动：`command + 拖动` (移动后方窗口) 

剪切：`command + C,command + option + V` (移动非复制文件)

**截图：**

全屏截图：`command + shift + 3`（ipad标注同步）

矩形截图：`command + shift + 4` （空格移动，option中心缩放） 

录屏工具：`command + shift + 5 `

截图剪贴板：`ctrl + command + shift + 4 `

**应用窗口：**

退出应用：`command  + q`

隐藏窗口：`command + h` 

关闭窗口：`command + w`

新建窗口：`command + n `

最小化窗口：`command + m`

新建标签页：`command + t`

隐藏其他窗口：`command + option + h `

全屏：`control + command + f` \ `fn + f`

**快捷方式：**

快速打开应用程序文件夹：`command  + shift + a`（或其他自定义快捷键）

`command  + shift + 点击应用在dock的图标`

显示/隐藏程序坞：`command + option + d`

**放大手势**

屏幕缩放/放大/缩小：`command + option 8/=/-`

屏幕放大/缩小： `control + 触控板上/下`
