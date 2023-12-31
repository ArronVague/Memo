# Memo
 A repo to store solutions to problems I've encountered on the way to learning.

New problems will be added to issues.

## Problems

### 在ubuntu 18.04/22.04使用clash-linux-amd64-v1.2.0（弃用，不方便）

**下载clash-linux-amd64-v1.2.0**

~~[clash下载地址 github](https://github.com/Dreamacro/clash/releases)~~（已跑路）

~~[别人下载好的](https://disk.pku.edu.cn/#/link/60D2F38BE69B49D11C6B32FEB32F31A3)~~（已失效）

```bash
cd ~

mkdir clash

cd clash
// 将下载好的clash安装包放到这个文件夹

gzip -d clash-linux-amd64-xx.gz
// 将clash-linux-amd64-xx.gz更换成你下载的版本

chmod +x clash-linux-amd64-xx
```

**配置clash**

在windows下获取clash的config.yaml和Country.mmdb

**获取config.yaml**

文件路径

```bash
C:\Users\你的名字\.config\clash\profiles
```

选择最新的.yml文件，如1694746034143.yml，注意不是list.yml。将这个文件复制一份，重命名为config.yaml。

**获取Country.mmdb**

文件路径

```bash
C:\Users\你的名字\.config\clash
```

看到Country.mmdb。

将刚才重命名的config.yaml和Country.mmdb放到ubuntu系统下。

```bash
mkdir    ~/.config/clash
sudo mv   Country.mmdb    ~/.config/clash
sudo mv    config.yaml     ~/.config/clash
```

**配置ubuntu网络代理**

配置系统网络代理Network Proxy

HTTP Proxy 127.0.0.1 7890

HTTPS Proxy 127.0.0.1 7890

Socks Host 127.0.0.1 7891

其余默认。

**运行**

```bash
cd ~/clash
./clash-linux-amd64-xx
```

注意：此时可以使用浏览器访问谷歌，但是终端网络不会经过clash。需要

```bash
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
```

且仅在当前终端生效。

**终端长期使用**

长期生效需要将上面两条命令写入~/.bashrc文件中，写到最后即可。

**配置APT代理（没试过）**

终端中的APT包管理器默认不使用系统代理。为了让APT也通过Clash进行网络连接，您需要编辑APT的配置文件。打开终端并运行以下命令编辑`apt.conf`文件：

```bash
sudo nano /etc/apt/apt.conf

// 在打开的文件中添加以下内容：
Acquire::http::Proxy "[http://127.0.0.1:7890/](http://127.0.0.1:7890/)";  
Acquire::https::Proxy "[http://127.0.0.1:7890/](http://127.0.0.1:7890/)";
```

### 在ubuntu 18.04/22.04使用Clash.for.Windows-0.20.39-x64-linux

最后一版clash，哀悼一秒。

**下载Clash.for.Windows-0.20.39-x64-linux**

[下载地址](https://archive.org/download/clash_for_windows_pkg)

下载Clash.for.Windows-0.20.39-x64-linux.tar.gz

放到ubuntu

执行解压命令

```bash
解压
tar -zxvf xxx.tar.gz
改下文件夹名
sudo mv Clash\ for\ Windows-0.20.39-x64-linux clash
```

**配置ubuntu网络代理**

配置系统网络代理Network Proxy

HTTP Proxy 127.0.0.1 7890

HTTPS Proxy 127.0.0.1 7890

Socks Host 127.0.0.1 7891

其余默认。

**运行**

```bash
cd clash
./cfw
```

**导入订阅链接**

和windows下使用没什么不同，懂得都懂。

### 多电脑使用相同虚拟机

[VMware虚拟机从一台电脑复制到另一台电脑](https://zhuanlan.zhihu.com/p/603483636#:~:text=%E5%9C%A8%E4%B8%80%E5%8F%B0%E7%94%B5%E8%84%91%E4%B8%8A,%E7%94%B5%E8%84%91%E4%B8%8A%EF%BC%8C%E7%9C%81%E6%97%B6%E7%9C%81%E5%8A%9B%E3%80%82)

### 虚拟机迁移后无法联网

虚拟机迁移后遇到突然连不上网的问题

1. 宿主windows中，settings -> Betwork & internet -> Advanced network settings，允许虚拟机的网络适配器

2. 虚拟机中

   ```bash
   sudo dhclient ens33
   ```

### vscode设置自动换行

linux/windows中 `ctrl + ,` 搜索word wrap，改为on

### Ubuntu 18.04/20.04默认全局缩放修改

[Ubuntu 20.04默认全局缩放修改，简单五步即可实现](https://blog.csdn.net/Pthumeru/article/details/119304019)

### win11使用wsl

**方法一：直接下载**

直接下载，会自动安装最新的ubuntu

```bash
wsl -l
```

**方法二：导入**

**导入**

```bash
wsl --import <Distribution Name> <InstallLocation> <FileName>
```

例如

```bash
wsl --import compilers D://Software/SoftwareData/compilers D://Software/SoftwareData/compilers/compilers.tar
```

**运行**

```bash
wsl -d <Distribution Name>
```

**设置用户账户**

```bash
NEW_USER=<USERNAME> // <USERNAME>替换成你的用户名
useradd -m -G sudo -s /bin/bash "$NEW_USER"
passwd "$NEW_USER"
```

**设定默认用户**

```bash
tee /etc/wsl.conf <<_EOF
[user]
default=${NEW_USER}
_EOF
```

#### 配置代理

**问题**

```bash
wsl: A localhost proxy configuration was detected but not mirrored into WSL. WSL in NAT mode does not support localhost proxies.
```

**方法一：开启TUN MODE**

把clash或其他代理客户端开TUN MODE。

**方法二：WSL2配置代理**

**新建脚本`proxy.sh`**

```sh
#!/bin/sh
hostip=$(cat /etc/resolv.conf | grep nameserver | awk '{ print $2 }')
wslip=$(hostname -I | awk '{print $1}')
port=7890
 
PROXY_HTTP="http://${hostip}:${port}"
 
set_proxy(){
  export http_proxy="${PROXY_HTTP}"
  export HTTP_PROXY="${PROXY_HTTP}"
 
  export https_proxy="${PROXY_HTTP}"
  export HTTPS_proxy="${PROXY_HTTP}"
 
  export ALL_PROXY="${PROXY_SOCKS5}"
  export all_proxy=${PROXY_SOCKS5}
 
  git config --global http.proxy ${PROXY_HTTP}
  git config --global https.proxy ${PROXY_HTTP}
 
  echo "Proxy has been opened."
}
 
unset_proxy(){
  unset http_proxy
  unset HTTP_PROXY
  unset https_proxy
  unset HTTPS_PROXY
  unset ALL_PROXY
  unset all_proxy
  git config --global --unset http.proxy
  git config --global --unset https.proxy
 
  echo "Proxy has been closed."
}
 
test_setting(){
  echo "Host IP:" ${hostip}
  echo "WSL IP:" ${wslip}
  echo "Try to connect to Google..."
  resp=$(curl -I -s --connect-timeout 5 -m 5 -w "%{http_code}" -o /dev/null www.google.com)
  if [ ${resp} = 200 ]; then
    echo "Proxy setup succeeded!"
  else
    echo "Proxy setup failed!"
  fi
}
 
if [ "$1" = "set" ]
then
  set_proxy
 
elif [ "$1" = "unset" ]
then
  unset_proxy
 
elif [ "$1" = "test" ]
then
  test_setting
else
  echo "Unsupported arguments."
fi
```

注意：其中第4行的`port`更换为自己的代理端口号。

- `source ./proxy.sh set`：开启代理
- `source ./proxy.sh unset`：关闭代理
- `source ./proxy.sh test`：查看代理状态

**任意路径下开启代理**

可以在`~/.bashrc`中添加如下内容，并将其中的路径修改为上述脚本的路径：

```bash
alias proxy="source /path/to/proxy.sh"
```

然后输入如下命令：

```bash
source ~/.bashrc
```

那么可以直接在任何路径下使用如下命令：

- `proxy set`：开启代理
- `proxy unset`：关闭代理
- `proxy test`：查看代理状态

**自动设置代理**

也可以在`~/.bashrc`添加如下内容，即在每次shell启动时自动设置代理，同样的，更改其中的路径为自己的脚本路径：

```bash
. /path/to/proxy.sh set
```

使用`curl`即可验证代理是否成功，如果有返回值则说明代理成功。

```bash
curl www.google.com
```

**参考文献**

[WSL2配置代理](https://www.cnblogs.com/tuilk/p/16287472.html)

### gitee的draw.io

没有适配长名字的仓库，提交.drawio时要求输入文件名，却因为仓库名太长而导致输入框隐藏。

直接检查元素找到那一块前端代码，将仓库名改短。

### Folder name

Avoid using spaces if possible. I tried to use path as

```bash
../../lab05/Priests and Devils/README.md
```

It worked in marktext, but not in github

The final solution is to replace spaces with `%20` beacause github will automatically replace spaces in path with `%20`.

### github markdown中latex块内公式换行

https://github.com/flysnow-org/maupassant-hugo/issues/21

懒得试，自行尝试。想要在github上渲染出来，会导致pdf很丑。为了兼顾导出的pdf和github上渲染出来的效果，直接开新块了。

### vscode跟随系统切换主题

settings搜索auto detect color scheme，勾上。

### github markdown中latex对于小于号<的渲染

又是一个逆天的问题。

可以渲染出`>`，就是不能渲染出`<`。只能用`\textless`。

### github管理unity项目

.gitignore中不能忽略.meta后缀的文件。新生成的.meta文件，guid与删除文件之前不同。

### Acrobat打开PDF时，出现“内容准备进度。正在准备文档以供阅读，请稍候。”状态：正在处理第 页，共 _ 页”

https://helpx.adobe.com/cn/acrobat/kb/message-content-preparation-progress-opening.html

### conda环境安装cvxopt

```bash
conda install -c conda-forge cvxopt
```

### vscode使用mysql

安装相关扩展。

![mysql_extension](pic/mysql_extension.png)

至此可以连接数据库

**format sql文件**

安装sql formatter。

`alt + shift + f`配置mysql默认format为sql formatter。

保存时可自动格式化。

### Ubuntu配置环境时老是出现依赖的套娃问题

听我的，用aptitude。

```bash
sudo apt-get update
sudo apt-get install aptitude
```

然后用aptitude替换apt-get。

### conda安装各种库时报错Solving environment: failed with initial frozen solve. Retrying with flexible solve.

更新conda（我用这个方法解决了）

```bash
conda update --all --yes
```

如果还没解决的话，尝试创建一个新的、比较纯净的虚拟环境，再安装。

### ubuntu使用pyenv管理python版本

要在Ubuntu上安装pyenv，可以按照以下步骤进行操作：

1. 更新系统软件包列表。打开终端并运行以下命令：

   ```bash
   sudo apt update
   ```

2. 安装pyenv的依赖项。运行以下命令：

   ```bash
   sudo apt install curl git
   ```

3. 使用curl命令安装pyenv。运行以下命令：

   ```bash
   curl https://pyenv.run | bash
   这将下载并运行pyenv的安装脚本。
   ```

4. 添加pyenv到你的bash配置文件。打开你的bash配置文件（例如：

   ```bash
   .bashrc
   ```

   ```bash
   .bash_profile
   ```

   或

   ```bash
   .zshrc
   ```

   ）并在末尾添加以下行：

   ```bash
   export PATH="$HOME/.pyenv/bin:$PATH"
   eval "$(pyenv init -)"
   eval "$(pyenv virtualenv-init -)"
   
   保存并关闭文件后，运行以下命令使配置文件生效：
   source ~/.bashrc
   ```

5. 验证pyenv是否成功安装。运行以下命令：

   ```bash
   pyenv --version
   
   如果显示pyenv的版本信息，则表示安装成功。
   ```

6. 安装Python版本。现在你可以使用pyenv来安装不同的Python版本。例如，要安装Python 3.9.0，运行以下命令：

   ```bash
   pyenv install 3.9.0
   
   等待安装完成。
   ```

7. 设置全局Python版本。如果你想将Python 3.9.0设置为全局Python版本，运行以下命令：

   ```bash
   pyenv global 3.9.0
   
   这将在系统中将Python 3.9.0设置为默认版本。
   ```

### windows系统powershell/terminal无法使用conda虚拟环境

```bash
conda init powershell
```

重启终端即可

### [Git merge & rebase 区别和用法](https://www.bilibili.com/video/BV1Vc411S7gv/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=212ff176b778171e26249f81cfb5bdbc)

## Remember

### 农的各种api

英雄信息、原画等。

https://blog.csdn.net/qq_39046854/article/details/118499126

英雄最低战力

https://zhuanlan.zhihu.com/p/520449999

### 充电宝选购

**10000 - 20000mAh（不含20000）**

- 京东京造JP266 ￥89.9
- 倍思自带线充电宝 ￥85
- 爱国者C10PD ￥79.9
- 华为66W充电宝 ￥339
- 品胜充电宝 ￥99
- 小米充电宝 33W 口袋版 ￥179
- 酷态科 10号超级电能棒 ￥199（听说是唯一真神）
- 紫米 迷你移动电源 ￥179
- 紫米充电宝 - 口袋版 ￥109
- 纽曼自带线充电宝 ￥69
- 羽博 二合一充电宝 ￥85
- 半岛铁盒A12充电宝 ￥59.9
- 机乐堂 充电宝 ￥149
- 机乐堂小果冻 ￥139
- 小米 自带线充电宝 ￥129
- OPPO 闪充充电宝 ￥179

**20000mAh以上**

- 绿联 PB201 ￥129
- ~~小米移动电源3~~ ￥199（没有数显）
- ~~京造 JP256~~ ￥89.9（没有数显）
- 罗马仕 Sense 6+ ￥99（性价比不如京造。接口多）
- 紫米20号移动电源 ￥374（100W pd快充）
- ~~科诺克 迷你充电宝~~ ￥69（疑似虚标）
- 倍思明充电宝 ￥159（似乎有点慢）
- 冇心幻音 充电宝 ￥178
- 哇牛能量块 3号 ￥179（轻、自带线）
- 睿量 充电宝 ￥229

**参考文献**

https://www.bilibili.com/video/BV1fN4y127BM/?spm_id_from=333.337.search-card.all.click&vd_source=212ff176b778171e26249f81cfb5bdbc

