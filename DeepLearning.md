# PyTorch

> 下载地址：https://pytorch.org/get-started/locally/

```shell
# 进入python
python
>>> import torch                 # 引入torch包是否报错
>>> torch.cuda.is_available()    # 是否可用电脑GPU
True
```

## 快速入门

> 理解pytorch相当于一个工具箱，这个package中充满各种各样的开发道具

`dir(torch)`：显示torch中有哪些“工具“

`help(torch.cuda.is_available)`：显示工具中方法的用法和作用





# Anaconda

> Windows：https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Windows-x86_64.exe
>
> Linux：https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh

**永久添加镜像源**

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/

## 环境管理命令：

- `conda create --name myenv`：创建一个名为`myenv`的新环境。
- `conda create --name myenv --prefix C:/user/xxx/myenv python=3.8.0`：在指定文件夹下创建环境
- `conda activate myenv`：激活名为`myenv`的环境。
- `conda deactivate`：退出当前环境。
- `conda env remove --name myenv`：删除名为`myenv`的环境。
- `conda env remove --prefix C:/user/xxx/myenv`：根据路径删除环境

## 包管理命令：

- `conda install jupyterlab`：在当前环境中安装jupyterlab包。
- `conda update jupyterlab`：更新jupyterlab包到最新版本。
- `conda remove jupyterlab`：从当前环境中卸载jupyterlab包。
- `conda list`：列出当前环境中安装的所有包。
- `conda search jupyterlab`：搜索可用的jupyterlab包版本。

## 频繁使用的其他命令：

- `conda info`：显示conda的系统和环境信息。
- `conda info --envs`：显示所有创建的环境。
- `conda config --show`：显示当前conda配置。
- `conda clean --all`：清理下载和缓存文件。
- `conda update conda`：更新conda到最新版本。
- `conda update anaconda`：更新Anaconda发行版到最新版本。

## 包和环境管理：

- `conda install --channel conda-forge pandas`：从特定的通道安装包。
- `conda install --file requirements.txt`：根据文件安装包列表。
- `pip install requests`：进入虚拟环境后下载模块中需要的包
- `pip list`：列出python中已经安装的包

# Jupyter

```shell
# 下载
conda install jupyterlab
# 进入jupyterlab界面：在指定文件夹下运行
jupyter lab
```

* `Enter`：编辑模式
* `Esc`：命令模式

**编辑模式快捷键**

* `Shift + Enter`：运行当前单元并选中下一单元
* `Ctrl + Enter`：运行当前单元但不移动光标
* `Alt + Enter`：运行当前单元并在下方插入新的单元
* `Ctrl + Z`：撤销操作
* `Ctrl + Y`：重做操作
* `Ctrl + S`：保存笔记本
* `Tab`：代码自动补全

**命令模式快捷键**

- `A`：在当前单元上方插入新单元
- `B`：在当前单元下方插入新单元
- `D, D`（按两次 D）：删除当前单元
- `Z`：恢复已删除单元
- `Y`：将单元类型设置为代码单元
- `M`：将单元类型设置为 Markdown 单元
- `H`：显示快捷键帮助
- `Shift + Space`：向上滚动（在单元之间导航）
- `Space`：向下滚动
