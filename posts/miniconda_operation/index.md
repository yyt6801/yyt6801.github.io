# Miniconda常用操作


# Miniconda实用操作

## Miniconda简介
[官网](https://www.anaconda.com/docs/getting-started/miniconda/main)介绍： *`Miniconda is a free, miniature installation of Anaconda Distribution that includes only conda, Python, the packages they both depend on, and a small number of other useful packages. If you need more packages, use the conda install command to install from thousands of packages available by default in Anaconda s public repo, or from other channels, like conda-forge or bioconda. `*

译：Miniconda是Anaconda发行版的一个免费的微型安装版，它只包括conda、Python、它们都依赖的包，以及少量其他有用的包。如果需要更多的包，可以使用conda install命令从Anaconda的公共仓库中默认提供的数千个包中进行安装，或者从其他渠道（如conda-forge或bioconda）进行安装。

## Miniconda功能及使用场景
Python 不同版本的包库可能存在兼容性问题，因此，使用 Miniconda 可以创建多个虚拟环境，每个环境使用不同的 Python 版本和库，从而避免版本冲突和依赖问题。

## 安装Miniconda
  * [清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/)
  * [官网下载地址](https://repo.anaconda.com/miniconda/)

    安装后需勾选配置到环境变量中 *`(Add Miniconda3 to my PATH environment variable)`* 。  
    也可手动添加，需添加如下三条环境变量：  
    * `D:\Software\Miniconda`  
    * `D:\Software\Miniconda\Scripts`  
    * `D:\Software\Miniconda\Library\bin`  

## 查看当前所有环境
```bash
conda info --env
```
或
```bash
conda info -e
```
或
```bash
conda env list
```

## 创建环境
创建环境时，可指定Python版本，如Python3.8
```bash
conda create -n env_name python=3.8
```

## 激活环境

```bash
conda activate env_name
```

## 退出环境

```bash
conda deactivate
```

## 删除环境

```bash
conda remove -n env_name --all
```

## 修改conda源
```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

## 查看当前配置的conda源
```bash
conda config --show-sources
```

## conda添加清华源
```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

## 删除源
```bash
conda config --remove channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

## 若在清华源中查不到，可以添加官方conda-forge
```bash
conda config --append channels conda-forge
```

## 安装某库/包

```bash
conda install package_name
```

## 查看所有已安装包

```bash
conda list
```

## 删除包

```bash
conda remove package_name
```

## 更新包

```bash
conda update package_name
```

## 清理包

```bash
conda clean --all
```

## 导出环境

```bash
conda env export &gt; environment.yml
```

## 导入环境

```bash
conda env create -f environment.yml
```




---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: http://localhost:1313/posts/miniconda_operation/  

