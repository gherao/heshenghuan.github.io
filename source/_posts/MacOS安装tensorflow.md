---
title: MacOS安装tensorflow
date: 2016-09-26 11:25:57
tags: Tensorflow
categories: 学习
---

## 背景

2016年9月24日的上海 EMNLP 2016 论文研讨会之行结束之后，愈发觉得深度学习在NLP领域内的应用广泛了起来。虽说自己也一直觉得深度学习在NLP任务上的可解释性一直不明了，有些任务它就是好但你说不出它为什么好，所以一直有些不敢试水。但现在觉得再不跟上步伐，可能就真的要被别人甩开了。

于是回到学校之后，决定开始学习TensorFlow。作为一个入口，慢慢学习如何用Deep Learnning进行自然语言处理。

## TensorFlow

![TensorFlow](http://img1.tuicool.com/zeIbauf.png!web)

选择TensorFlow作为入门的工具，是经过同组师兄弟介绍才决定的。回程的动车上他们介绍说这个工具的学习成本比较低，但是又不缺乏深度。想要快速搭建起一个深度学习的学习环境的话，TensorFlow是个不错的选择。

而且作为谷歌的产品，我还是有一种迷之崇拜的。安装过程也有些许不顺，都与OS X系统本身有些问题，所以写下这篇文章，整理出来备忘。

## 安装

### 安装Pip

根据TensorFlow官网给出的安装步骤，首先需要进行的是Pip的安装：

```shell
$ sudo easy_install pip
```

我的电脑上本身就有Pip，所以我用一下命令进行了更新：

```shell
$ sudo pip install —upgrade pip
```

这一步没有任何问题。

### 安装TensorFlow

因为我的MacBook Pro是15年中的中配版本，所以选择的是Mac-CPU-only的安装包，这里推荐在TensorFlow的[github仓库](https://github.com/tensorflow/tensorflow)的首页中下载最新的包。

```shell
$ sudo easy_install --upgrade six
$ sudo pip install --upgrade ~/Download/tensorflow-0.10.0-py2-none-any.whl
```

这里的.whl包就是TensorFlow的安装文件，建议直接下载到本地然后通过给定文件路径安装（如上所以）。

## 问题

### [Error 1] Operation not permitted

如果你的Mac系统是低于10.11，即OS X El Capitan，这一部分的问题应该是不会出现的。如果是的话，你会发现在上一步pip install的命令执行过程中，会报出类似如下错误：

> Found existing installation: numpy 1.8.0rc1
>  DEPRECATION: Uninstalling a distutils installed project (numpy) has been deprecated and will be removed in a future version. This is due to the fact that uninstalling a distutils project will only partially uninstall the project.
>  Uninstalling numpy-1.8.0rc1:Exception:Traceback
>  (most recent call last):
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/basecommand.py", line 211, in main
>  status = self.run(options, args)
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/commands/install.py", line 311, in run
>  root=options.root_path,
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_set.py", line 640, in install
>  requirement.uninstall(auto_confirm=True)
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_install.py", line 716, in uninstall
>  paths_to_remove.remove(auto_confirm)
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/req/req_uninstall.py", line 125, in remove
>  renames(path, new_path)
>  File "/Library/Python/2.7/site-packages/pip-7.1.2-py2.7.egg/pip/utils/__init__.py",
>  line 315, in renames
> shutil.move(old, new)
>  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 302, in move
>  copy2(src, real_dst)
>  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 131, in copy2
>  copystat(src, dst)
>  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/shutil.py", line 103, in copystat
>  os.chflags(dst, st.st_flags)OSError:
>  **[Errno 1] Operation not permitted**: '/var/folders/5n/vbm997m56xg3kw67y6bccn2m0000gn/T/pip-4tcBsd-uninstall/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/numpy-1.8.0rc1-py2.7.egg-info'。

可以不用看具体，我加粗了其中报错的错误信息，Operation not permitted。这是由于OS X El Capitan中，在内核下引入了Rootless机制，以下路径：

> /System
>
> /bin
>
> /sbin
>
> /usr (except /usr/local)

均属于Rootless范围，即使root用户无法对此目录有写和执行权限，只有Apple以及Apple授权签名的软件（包括命令行工具）可以修改此目录。所以不关闭Rootless是无法完成安装的。

### 如何关闭OS X El Capitan中的Rootless

以下内容是quora上作者[Amir Salah](https://www.quora.com/profile/Amir-Salah)的回答[How do I turn off the rootless in OS X El Capitan 10.11?](https://www.quora.com/How-do-I-turn-off-the-rootless-in-OS-X-El-Capitan-10-11)的中文简单翻译。如果你英语没问题的话，可以阅读原始答案，给作者点个赞！

这里需要重启你的Mac，并在屏幕变黑时按住`command+R`，直到Apple logo出现在屏幕上。此时你的电脑进入了Recovery模式，可以看到如下界面。

![Revovery](https://qph.ec.quoracdn.net/main-qimg-e02894ba6ddf5d617c98c339ac893d59?convert_to_webp=true)

点击Utilities（实用工具）-> Terminal（终端），输入如下命令：

```shell
$ csrutil disable
```

回车后应该可以看到如下信息：

![](https://qph.ec.quoracdn.net/main-qimg-c33df35abcbf8c9179d80f74ba0240e4?convert_to_webp=true)

这时重启你的Mac，再重新安装TensorFlow即可。**记得在安装完成后再次进入Recovery模式，在终端中用命令`csrutil enable`重新开启Rootless哦。**

### TensorFlow安装完成但import时出错

正常情况下，在终端中进入Python，import tensorflow没有任何输出即为安装成功。但我使用pip命令安装完成后，却无法在python中导入tensorflow，并伴有如下错误：

> \>\> import tensorflow
> RuntimeError: module compiled against API version 0xa but this version of numpy is 0x9
> Traceback (most recent call last):
>   File "<stdin>", line 1, in <module>
>   File "/Library/Python/2.7/site-packages/tensorflow/\_\_init\_\_.py", line 23, in <module>
>
>     from tensorflow.python import *
>   File "/Library/Python/2.7/site-packages/tensorflow/python/\_\_init\_\_.py", line 49, in <module>
>     from tensorflow.python import pywrap_tensorflow
>   File "/Library/Python/2.7/site-packages/tensorflow/python/pywrap_tensorflow.py", line 28, in <module>
>     _pywrap_tensorflow = swig_import_helper()
>   File "/Library/Python/2.7/site-packages/tensorflow/python/pywrap_tensorflow.py", line 24, in swig_import_helper
>     _mod = imp.load_module('_pywrap_tensorflow', fp, pathname, description)
> ImportError: numpy.core.multiarray failed to import

这是由于numpy包的版本与TensorFlow所依赖的版本不一致所导致的。但是pip安装时已经更新了所有依赖的包了，问题就出在苹果系统自带的Python的环境变量$PYTHONOPATH上。

pip安装TensorFlow时，根据依赖关系更新的模块都安装在路径“/Library/Python/2.7/site-packages”下，而苹果自带的Python安装的numpy在“/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python”下，并且诡异的是加载路劲中extras的顺序要靠前。

于是每次进入python导入的numpy都是没有更新的旧版本，这就是问题的原因所在。

```shell
$ python -c "import numpy; print numpy.__file__"
```

可以用来查看导入的numpy究竟是在哪个目录下。这个问题也很好解决，更改下PYTHONPATH中的加载顺序即可。

```shell
$ python -c "import sys; print '\n'.join([pth for pth in sys.path])"

/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python27.zip
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-darwin
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac/lib-scriptpackages
/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-tk
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-old
/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload
/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/PyObjC
/Library/Python/2.7/site-packages
/Library/Python/2.7/site-packages/six-1.10.0-py2.7.egg
/Library/Python/2.7/site-packages/numpy-1.11.2rc1-py2.7-macosx-10.11-intel.egg
```

查看当前有哪些加载路径，然后在/etc/profile或者~/.bashrc中输出环境变量，把最后三条路径调整到最前面即可：

```shell
$ export PYTHONPATH=/Library/Python/2.7/site-packages:/Library/Python/2.7/site-packages/six-1.10.0-py2.7.egg:/Library/Python/2.7/site-packages/numpy-1.11.2rc1-py2.7-macosx-10.11-intel.egg:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python27.zip:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-darwin:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac/lib-scriptpackages:/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-tk:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-old:/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload:/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/PyObjC  
```

**注意路径之间有冒号":"，切记不可换行**。重启终端后，在进入Python导入TensorFlow就没有任何问题啦。