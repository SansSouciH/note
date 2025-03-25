# DeepLearning

![image-20250305155258182](img\image-20250305155258182.png)

## 深度学习基础

机器学习的基本单位：N维数组

* 0-d（标量）：一个类别
* 1-d（向量）：一个特征向量
* 2-d（矩阵）：一个样本-特征矩阵
* 3-d（图片）：RGB图片，宽x高x通道
* 4-d（批量图）：批量大小x宽x高x通道
* 5-d（视频批量）：批量大小x时间x宽x高

`tensor`：张量，神经网络训练的基本单位，进入训练时所有的其他类型都会被转换为tensor类型加入训练模型得出结果

`grad`：梯度，多元实值函数对应的一个向量值函数；它代表多元函数的值改变“最快”的方向。

# PyTorch

> 下载地址：https://pytorch.org/get-started/locally/

```shell
# 进入python
python
>>> import torch                 # 引入torch包是否报错
>>> torch.cuda.is_available()    # 是否可用电脑GPU
True
```

> 理解pytorch相当于一个工具箱，这个package中充满各种各样关于深度学习的开发工具

## Tensorboard

> 在训练过程中展示Loss、Accuracy等指标可视化工具

```shell
tensorboard --logdir=生成的log文件夹名 --port=6006（默认6006）
```

```python
from torch.utils.tensorboard import SummaryWriter
SummaryWriter(log_dir='./logs')
```

**SummaryWriter的常用方法**

| 方法              | 功能                           | 常用参数                                      |
| ----------------- | ------------------------------ | --------------------------------------------- |
| `add_scalar()`    | 记录单个标量                   | `tag`, `scalar_value`, `global_step`          |
| `add_image()`     | 记录图像（要求channels为1或3） | `tag`, `img_tensor`, `global_step`            |
| `add_images()`    | 批量记录图像（同要求1或3）     | `tag`，`imgs_tensor`，`global_step`           |
| `add_histogram()` | 记录直方图                     | `tag`, `values`, `global_step`                |
| `add_graph()`     | 可视化计算图                   | `model`, `input_to_model`                     |
| `add_text()`      | 记录文本                       | `tag`, `text_string`, `global_step`           |
| `add_pr_curve()`  | 记录 PR 曲线                   | `tag`, `labels`, `predictions`, `global_step` |
| `close()`         | 关闭 writer                    | 无                                            |

**add_scalar**：最常用！记录 loss、acc 等曲线

**add_image**：看输入、特征图长啥样

**add_graph**：看模型结构图

**add_histogram**：看参数分布（如权重、激活值）

**add_text**：记录一些备注信息

## TorchVision

> PyTorch官方视觉工具包，提供三个核心功能：datasets（数据集模块）、models（预训练模型模块）、transforms（图像变换模块）

### Datasets

常用的 **公开数据集**（比如 MNIST、CIFAR-10、ImageNet 等）都能直接通过 `torchvision.datasets` 下载和加载

> 官方数据集地址：https://pytorch.org/vision/stable/datasets.html

```python
import torchvision

dataset_transform = torchvision.transforms.Compose([
    torchvision.transforms.ToTensor()
])
train_set = torchvision.datasets.CIFAR10(root='./data', train=True, transform=dataset_transform, download=True)
test_set = torchvision.datasets.CIFAR10(root='./data', train=False, transform=dataset_transform, download=True)
```

**DataLoader**

`DataLoader` 是 PyTorch 中用于 **批量加载数据** 的工具。它的主要作用是：

1. 将 Dataset 中的数据分批（batch）读取。
2. 自动打乱（shuffle）数据（可选）。
3. 支持多线程并行读取数据（`num_workers`）。
4. 每次迭代（`for batch in dataloader:`）返回一个 batch 的数据，便于训练时使用。

它解决了训练时「逐张加载太慢」或「数据不打乱不利于模型泛化」的问题。

```python
from torch.utils.tensorboard import SummaryWriter

data_loader = torch.utils.data.DataLoader(dataset, batch_size=4, shuffle=False, num_workers=0, drop_last=False)
writer = SummaryWriter(log_dir = './logs')
step = 0
for data in data_loader:
    imgs, labels = data
    writer.add_images('test_data', imgs, step)   #批量添加图片
    step = step + 1
writer.close()
```

`dataset`: 自定义或内置的 Dataset（例如 `torchvision.datasets.MNIST`）

`batch_size`: 每个 batch 的样本数

`shuffle`: 是否在每个 epoch 开始时打乱数据

`num_workers`: 用多少个子进程加载数据，0 表示主进程

`drop_last`: 是否丢弃最后一个不足 batch_size 的 batch

### Models

直接提供了**各种经典的 CNN 网络架构和预训练模型**，比如 ResNet、VGG、AlexNet、MobileNet 等



### Transforms

> 配合 `datasets` 使用，负责**数据增强、归一化、转换成 Tensor 等操作**

**常用操作**

`transforms.ToTensor()`：把 **PIL.Image** 或 **numpy.ndarray** 转成 Tensor（同时归一化到 0~1）。

`transforms.Normalize(mean, std)`：图像的 **标准化**，均值和方差。

`transforms.Resize()`：**缩放图片尺寸**。

`transforms.RandomCrop()`：**随机裁剪**，做数据增强。

`transforms.RandomHorizontalFlip()`：**随机水平翻转**。

```python
from torchvision import transforms

transform = transforms.Compose([
    transforms.Resize((224, 224)),       # 调整图片大小
    transforms.RandomHorizontalFlip(),   # 随机水平翻转
    transforms.ToTensor(),               # 转成 PyTorch Tensor（[0,255]转[0,1]）
    transforms.Normalize((0.5,), (0.5,)) # 数据归一化
])
```

**Normalize**

> 对 Tensor 格式的图片进行 **标准化**，每个通道都按指定的均值、标准差做归一化。
>
> 公式：`output = (input - mean) / std`

```python
# 用于RGB 3通道图片。(像素点 - 0.5)/0.5
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize(mean=(0.5, 0.5, 0.5), std=(0.5, 0.5, 0.5))
])
```

* 加速神经网络训练
* 防止某个通道的像素值过大，导致模型对该通道更敏感
* 与预训练模型保持一致（如 ResNet、VGG 等）

## Torch.nn

> Neural network（神经网络）通常由输入层、隐藏层、输出层构成，由计算机模拟神经元电信号运作。

### Conv2d

* 实现 **二维卷积层** 的模块，主要用于图像、特征图等 2D 数据的特征提取。在图像处理中，卷积层可以自动学习图像中的 **边缘、纹理、形状** 等特征。

* 它的作用是将输入的 **多通道二维数据**（如图像）与一组可学习的 **卷积核（filters）** 进行滑动卷积操作，输出新的特征图（Feature Maps）。

```python
torch.nn.Conv2d(
    in_channels,
    out_channels,
    kernel_size,
    stride=1,
    padding=0,
    dilation=1,
    groups=1,
    bias=True,
    padding_mode='zeros'
)
```

**常用参数详细解释**

| 参数           | 含义                                           | 示例                                                         |
| -------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `in_channels`  | 输入特征图的通道数                             | 如果是 RGB 图像，`in_channels=3`；灰度图像，`in_channels=1`  |
| `out_channels` | 卷积核（filter）的数量，决定输出特征图的通道数 | `out_channels=16`，表示输出 16 个通道                        |
| `kernel_size`  | 卷积核的尺寸，通常是一个整数或 tuple           | `kernel_size=3` 代表 3x3 卷积核                              |
| `stride`       | 步幅（卷积窗口每次移动的步长）                 | 默认 `stride=1`，也可以设置为 `(2,2)` 表示高和宽方向步幅     |
| `padding`      | 填充，给输入特征图的边缘填充 0                 | `padding=1` 会在输入周围补一圈 0                             |
| `dilation`     | 卷积核元素之间的间距                           | 默认 `dilation=1` 表示卷积核是连续的，`dilation=2` 表示膨胀卷积 |
| `groups`       | 分组卷积                                       | `groups=1` 为普通卷积，`groups=in_channels` 为 depthwise 卷积 |
| `bias`         | 是否加上偏置项                                 | `bias=True` 默认加偏置                                       |
| `padding_mode` | 填充模式                                       | 常用 `'zeros'` (默认)，也支持 `'reflect'`, `'replicate'` 等  |

- **输入输出 shape**：
   输入的 shape 通常是 `[batch_size, in_channels, H, W]`，输出是 `[batch_size, out_channels, H_out, W_out]`。

- **H_out / W_out 的计算公式**：

  ```ini
  H_out = (H + 2 * padding - dilation * (kernel_size - 1) - 1) / stride + 1
  W_out = (W + 2 * padding - dilation * (kernel_size - 1) - 1) / stride + 1
  ```

  结果向下取整。

- **卷积核数量 = out_channels**
   每个卷积核负责输出一个通道，`out_channels=16` 表示有 16 个卷积核，输出 16 个特征图。

### MaxPool2d

> 神经网络：卷积 -> ReLu -> 池化

主要作用：

* **下采样（降维）**：减少 feature map 的空间尺寸（H 和 W），减小计算量。

* **提取主要特征**：通过**取局部窗口内的最大值，保留最强特征**，去除冗余。
* **防止过拟合**：降低特征图分辨率，简化模型，提高泛化能力。

```python
torch.nn.MaxPool2d(kernel_size, 
                   stride=None, 
                   padding=0, 
                   dilation=1, 
                   return_indices=False, 
                   ceil_mode=False)
```

**参数解释**

| 参数             | 含义                                               |
| ---------------- | -------------------------------------------------- |
| `kernel_size`    | 池化窗口大小，比如 `2` 表示 `2x2` 窗口。           |
| `stride`         | 步长，默认等于 `kernel_size`，也就是窗口滑动步幅。 |
| `padding`        | 池化前四周补零。通常用得不多，卷积更常用。         |
| `dilation`       | 池化窗口元素之间的间距，默认 `1`。                 |
| `return_indices` | 是否返回最大值的索引（用于反池化 `MaxUnpool2d`）。 |
| `ceil_mode`      | 是否向上取整计算输出大小。                         |

### ReLU

`ReLU(x)=max(0,x)`

即：

- 如果输入 x>0，输出为 x
- 如果输入 x≤0，输出为 0

**（1）非线性激活**

神经网络是由多层线性运算（如矩阵乘法、卷积等）组成的，如果不引入非线性激活，多个线性变换的叠加仍然是线性的，网络无法逼近复杂的非线性函数。

ReLU 作为激活函数，打破了线性，帮助网络学习非线性的特征。

**（2）计算简单高效**

相比 Sigmoid 或 Tanh 这类函数，ReLU 只涉及简单的 `max(0, x)` 计算，没有复杂的指数运算，计算开销非常低，提升了训练速度。

**（3）缓解梯度消失问题**

- **Sigmoid** 和 **Tanh** 容易在深层网络中导致梯度消失（梯度接近 0）。
- ReLU 在 x>0x > 0x>0 时，梯度恒为 1，这使得梯度能在前向传播和反向传播中更好地传递，缓解了梯度消失问题。

**（4）稀疏性（Sparsity）**

当输入小于等于 0 时，ReLU 的输出为 0。这会导致很多神经元在某些输入下被“抑制”，使得网络变得更稀疏（只有部分神经元被激活），稀疏性有助于提升模型的泛化能力。

### Linear

**`nn.Linear`** 是 PyTorch 中的**全连接层**（也叫**线性层**或**仿射变换层**），它的主要作用就是：

> **对输入数据做一个线性变换（矩阵乘法 + 加偏置）**

数学上等价于：

`y=xWT+by = xW^T + by=xWT+b`

- **x** 是输入向量
- **W** 是权重矩阵（需要学习）
- **b** 是偏置向量（需要学习）
- **y** 是输出向量

### Loss

**L1Loss**对异常值不敏感，适合用于对噪声不那么敏感的任务。鲁棒性比MSE更好，梯度永远是+-1

**MSELoss**对较大的误差非常敏感，因为平方会放大误差。收敛速度更快，梯度和误差成比例

**CrossEntropyLoss** 是一种常见的损失函数，广泛用于分类问题中，尤其是 **多分类** 和 **二分类** 问题。它衡量的是 **预测概率** 与 **真实标签** 之间的差异。

- **交叉熵** 是一种用于评估两个概率分布之间差异的度量。
- 在机器学习中，交叉熵损失函数主要用于衡量神经网络输出的概率分布（预测值）和实际类别标签之间的差异。

---

**鲁棒** ≈ 抗干扰能力强

**噪声** ≈ 数据中的“误差、异常”

**收敛快/慢** ≈ 学习速度快慢

**梯度恒定** ≈ 梯度不受误差大小影响

**梯度 ∝ 误差** ≈ 误差大 → 梯度大 → 学得快

### Save&Load

`torch.save()、torch.load()`

**保存和加载实例**

```python
# 保存方式1，模型结构+模型参数
torch.save(vgg16_train, 'vgg16_model.pth')
# 保存方式2，模型参数（官方推荐）
torch.save(vgg16_train.state_dict(), 'vgg16_model_state.pth')
# 加载模型1，模型结构+模型参数
model = torch.load('vgg16_model.pth')
print(model)
# 加载模型2，模型参数
vgg16 = torchvision.models.vgg16()
vgg16.load_state_dict(torch.load('vgg16_model_state.pth'))
print(vgg16)
```

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
