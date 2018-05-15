# Mac RAR Service 使用说明

鉴于Mac OS系统自身并不支持rar压缩/解压缩文件，找来的各种rar解压缩软件要么就是收费，要么就是难用，所性直接使用Mac的 **“自动操作”（Automator Workflow）** 写了个 **“服务”** ，以便像Windows一样快捷的压缩和解压rar文件，详细使用方式如下：

## 一、安装`rar`、`unrar`软件包

话不多说，直接使用Homebrew来完成安装，先安装`rar`，命令如下：

```sh
brew install caskroom/cask/rar
```

然后安装`unrar`，命令如下：

```sh
brew install unrar
```

**注意⚠️：** 此过程在天朝可能会较长，请 **~~“耐心等待”~~** **（用心翻墙）** 谢谢。

关于Homebrew，不解释，请不熟悉的小伙伴自行谷歌，或参阅：[Homebrew官网](https://brew.sh/index_zh-cn)

## 二、安装服务文件

直接将下载下来的两个服务文件拷贝到如下路径：`/Users/电脑用户名/Library/Services/`，将其中`电脑用户名`替换成自己电脑的用户名即可，如下图：

![rar_service](http://onmw6wg88.bkt.clouddn.com/15263711527056.jpg)

## 三、使用方式

#### 1. rar压缩
在`Finder`中选中要压缩的文件夹，右键，服务，就会看到有`添加到RAR压缩文件`，选择它就就可以将选中的文件夹压缩为rar压缩包了，如下图：

![15263752901984](http://onmw6wg88.bkt.clouddn.com/15263752901984.jpg)

#### 2. rar解压缩

在`Finder`中选中要解压缩的rar文件，右键，服务，就会看到有`解压缩RAR到当前文件夹`，选择它就就可以将选中的rar文件解压缩出来了，如下图：

![rar解压](http://onmw6wg88.bkt.clouddn.com/15263708614915.jpg)

#### 四、总结

使用 **“自动操作”** 编写快捷服务的时候，这个 **“自动操作”** 的机器人它只会搜索Mac系统默认的命令路径，并没有包含 `/usr/local/bin` 这个路径，关于为什么要这个路径就是基本的 Linux 相关知识，简单的说就是我们通过例如 brew，pip 等包管理工具安装的软件包基本都会链接或直接在改目录下生成命令执行文件，所以使用 **“自动操作”** 编写一个服务如果需要使用自行安装的软件包的时候需要在服务开头添加环境变量，并使之生效，否则类似 command not found 这样的错误不要太容易就发生了。

添加环境变量脚本如下：

```sh
PATH=/usr/bin:/usr/local/bin export PATH
```

使环境变量生效则用source命令，脚本如下：

```sh
if [ -x /usr/libexec/path_helper ]; then
    eval `/usr/libexec/path_helper -s`
fi
if  [ -f "$HOME"/.profile ]; then
    source "$HOME"/.profile
elif [ -f "$HOME"/.bash_profile ]; then
    source "$HOME"/.bash_profile
elif [ -f "$HOME"/.bashrc ]; then
    source "$HOME"/.bashrc
fi
```

这里使用这段脚本加载环境变量主要是因为Mac系统的环境变量，加载顺序为：

> 1. /etc/profile 
> 2. /etc/paths 
> 3. ~/.bash_profile 
> 4. ~/.bash_login 
> 5. ~/.profile 
> 6. ~/.bashrc

其中`1./etc/profile`和`2./etc/paths`是系统级别的，系统启动就会加载，一般不对这两个地方做操作。后面几个是当前用户级别的环境变量，但是值得注意的是 `3. ~/.bash_profile` `4. ~/.bash_login` `5. ~/.profile` 是按照从前往后的顺序读取的，也就是说如果 `3. ~/.bash_profile` 存在，则后面几个就会被忽略，不读了，如果 `3. ~/.bash_profile` 不存在，才会以此类推的读取后面的文件。最后的 `6. ~/.bashrc` 是没有上述规则的，它是 `shell bash` 打开的时候载入的。

参考资料：

[Creating Shell Script Actions](https://developer.apple.com/library/content/documentation/AppleApplications/Conceptual/AutomatorConcepts/Articles/ShellScriptActions.html)

[My Automator Workflow fails because it fails to find the git command within the 'Run Shell Script' command](https://apple.stackexchange.com/questions/97502/my-automator-workflow-fails-because-it-fails-to-find-the-git-command-within-the)


