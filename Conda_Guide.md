# Conda的安装、配置和使用
## 什么是Conda、Miniconda、Anaconda
- **Conda** 是一个包和环境管理的工具。支持Windows、macOS和Linux。Conda可以快速的安装、运行和更新包和相关的依赖。Conda也可以轻易地创建、保存、加载和转换环境。
- **Anaconda** 是一个用于科学计算的 Python 发行版，支持 Linux, Mac, Windows, 包含了conda、conda-build、Python和众多科学计算的包及其依赖。
- **Miniconda** 是一个 Anaconda 的轻量级替代，默认只包含了 conda，Python 和一些它们所以依赖的包。
![什么是conda](imgs/conda.png#pic_center=x30)

## 为什么要使用Conda
一个典型的python 项目会使用多个包来完成其功能。其中一些包也可能被其他项目所使用（共享）。
项目之间共享的包可能会引起冲突。
比如，我们有两个项目P1和P2，P1使用NumPy 1.2版本，而P2需要NumPy 1.3版本，一个环境中存在两个版本就可能导致冲突。
解决这个问题的办法就是使用虚拟环境。我们可以为每个项目分别创建一个独立的虚拟环境，来隔离包冲突。

常用的python虚拟环境管理工具有：

1. Virtualenv
2. Conda
3. pipenv
4. venv
   
通过使用这些工具，我们可以很容易的创建虚拟环境。

## Conda的安装和配置
miniconda官网：[https://www.anaconda.com/docs/getting-started/miniconda/main](https://www.anaconda.com/docs/getting-started/miniconda/main)

>由于Anconda和Miniconda的服务器都在国外，下载速度有点感人。我们可以使用清华的镜像：[https://www.anaconda.com/docs/getting-started/miniconda/main](https://www.anaconda.com/docs/getting-started/miniconda/main)

![安装conda](imgs/installation.png#pic_center=x30)

>**注意事项**
>- 最好通过 Anconda Prompt(conda) 使用conda。
>- 如果你想在普通的cmd.exe中使用conda，需要将conda添加到系统的PATH环境变量中。具体来说，对于conda4.6之后的版本是将 `{conda的安装目录}\condabin` 添加到PATH中。注意不是Scripts，这是conda 4.6之后的一个改变。
原因可以看[Conda command is not recognized on Windows 10](https://stackoverflow.com/questions/44597662/conda-command-is-not-recognized-on-windows-10)中 Simba 的回答。简单来说，就是之前将`Scripts`添加到PATH，它不仅将conda命令暴露给cmd，同时也会将base环境的python都暴露来给cmd。这样我们之前python就会被覆盖，无法使用。而`condabin`只会将命令暴露给cmd。

## 配置清华的channel
为什么要配置清华的channel呢，我们先解释一下conda的工作流程：

1. 我们输入命令，告诉conda要安装的package
2. conda从channel中下载我们指定的package。（channel是一个托管着很多package的仓库）

默认的channel服务器在国外下载缓慢，所以我们可以配置成清华的channel。
步骤：

1. 生成.condarc 的文件：可以借助一下命令生成.condarc文件。这个文件会生成在你用户目录下。
    ```
    conda config --set show_channel_urls yes
    ```
2. 修改.condarc内容：打开.condarc文件，将[https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)中的配置信息粘贴进去。
3. 运行一下命令，清除索引缓存，保证用的是镜像站提供的索引。
    ```
    conda clean -i 
    ```

## conda的使用

### 创建conda环境
```
conda create --name {env_name} {python=x.x.x} package1 package2
```
### 激活环境
```
conda activate {env_name}
```
### 停用当前环境
```
conda deactivate
```
### 安装包packages
一旦激活了环境，你就可以使用conda和pip在当前环境下安装你所需要的包。在conda环境中，不建议使用pip。

**使用conda**
```
conda install pkg_name1=x.x.x pkg_name2=x.x.x
```

**使用pip**
```
pip install pkg_name1==x.x.x pkg_name2==x.x.x
```

**或者根据requirements.txt文件，安装多个包**
```
pip install -r requirements.txt
```

### 查看环境中安装的包

**查看当前环境中的包**
```
conda list	
```

**查看指定环境中的包**
```
conda list -n {env_name}
```

**查看环境列表**
```
conda env list
conda info --envs # --envs可以简写成 -e
```

### 删除环境
首先要确保你不在该环境中。
```
conda env remove -n env_name
```

### 创建一个相同的环境
要创建与现有环境相同的环境，需要显式地创建要复制的环境的规范文件，并在创建新环境时使用它。

步骤一：创建一个spec file
```conda list --explicit > spec-file.txt```

步骤二：
```conda create --name myenv --file spec-file.txt```

你可以在同一台机器或者不同的机器上，做此操作。
如果你想在某环境中安装`spec file`文件指定的包，运行以下命令。
```
conda install --name env_name --file spec-file.txt
```

### 跨平台分享环境（推荐）
当您尝试在另一个系统/平台中复制您的环境时，存在一个常见的问题。
当您创建环境和包时，假设您运行如下命令。
这将下载并安装许多其他依赖包，以便使您想要安装的包能够工作。
这很容易引入不兼容的软件包。
所以，可以采用如下方法：
```
conda env export --from-history > environment.yml
```

添加`--from-history`标志将只安装您使用conda要求安装的包。它将不包括依赖包或使用任何其他方法安装的包。

传入`environment.yml`文件，别人就可以重新创建你的环境。
```
conda env create -f environment.yml
```

### 环境回滚
Conda维护了您对环境所做更改的历史记录，这里的更改是指您使用Conda命令所做的更改。它允许您通过使用修订号revision numbers回滚。

激活环境后，首先使用它查看修订和修订号。
```
conda list --revisions
```

然后，回滚。
```
conda install --rev {revison_num}
```
---
>参考：https://juejin.cn/post/7262280335978987557
