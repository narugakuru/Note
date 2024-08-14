```
nohup bash -c 'cd data && mkdir coco && cd coco && wget http://images.cocodataset.org/zips/val2017.zip' > output.log 2>&1 &
```
pip freeze > requirements.txt

- d2l 动手学深度学习3.8
- apex tensorrt模型的环境3.7
- fps onnx模型3.10
- model 数学建模环境3.10
- tf tensorflow没有使用
- yolo torch2.0+cu118和onnx
- qt，制作qfluent工具
- side,制作qfluent工具
- aoe，帝国脚本

# 虚拟环境

torch安装
[download.pytorch.org/whl/torch_stable.html](https://download.pytorch.org/whl/torch_stable.html)

### fluent
conda create --name qt python=3.10.14 -y

### vit

[【pytorch】Vision Transformer实现图像分类+可视化+训练数据保存_vision transformer训练自己的数据-CSDN博客](https://blog.csdn.net/weixin_51331359/article/details/124514770)

[从零开始手把手搭建Vision Transformers(Pytorch版本) (qq.com)](https://mp.weixin.qq.com/s?__biz=MzU3ODk2Njc5Mg==&mid=2247492959&idx=1&sn=13a39fb0dbf826fe02b563c2ec71eafb&chksm=fd6fe138ca18682e13c3361f6d530e3d27ea4aeea73615940185188d7bc06905aee17020ae24&token=1443126529&lang=zh_CN#rd)


```
pip install lightning=2.1 对应torch2.1以下

conda activate python=3.9 -y 
```

### model

```python
conda create --name model python=3.10 -y
pip install scikit-learn pandas numpy seaborn matplotlib jupyter openpyxl lightgbm xgboost
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
```

### colab

Google Colab默认提供了GPU加速，因此您无需额外配置GPU版本的PyTorch。此外，Colab中的Python版本为3.7，您可能无法直接指定Python版本为3.8。但是，大多数情况下，3.7版本也可以满足PyTorch和其他库的要求。
```python
!pip install torch==1.9.0+cu111 torchvision==0.10.0+cu111 torchaudio==0.9.0 -f https://download.pytorch.org/whl/torch_stable.html
!pip install d2l==0.17.6 seaborn openpyxl scikit-learn

import torch
print(torch.__version__)

```

### LangChain

```bash
conda create --name langchain python=3.11 -y
pip install -r requirements.txt
pip install -r requirements_api.txt
pip install -r requirements_webui.txt
```

### d2l

```python

conda create --name d2l python=3.8 -y

pip install d2l==0.17.6 seaborn openpyxl scikit-learn

pip install torch 
pip install torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

pip install torch==2.0.0+cu118 torchvision==0.15.1+cu118 torchaudio==2.0.1 -i https://pypi.tuna.tsinghua.edu.cn/simple
# conda安装必然报错
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
# 升级conda
conda update -n base conda
```

### tensorflower

只有cuda11.2能用GPU版本，其他已被移除
[windows安装cuda 11.8以及tensorflow-gpu 2.6_cuda11.8安装_月司的博客-CSDN博客](https://blog.csdn.net/yue81560/article/details/127931471)
```python
conda create --name tf python=3.8 -y
#sklearn是scikit-learn的缩写
pip install d2l
pip install keras seaborn openpyxl lxrd scikit-learn
pip install tensorflow
```

### fps

```python
conda create --name fps python=3.10 -y

pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

pip install mss
```

# 环境管理

### 环境变量

```
;D:\Environment\anaconda3;D:\Environment\anaconda3\Scripts;D:\Environment\anaconda3\Library\bin;D:\Environment\anaconda3\Library\mingw-w64\bin

;E:\Environment\anaconda3;E:\Environment\anaconda3\Scripts;E:\Environment\anaconda3\Library\bin;E:\Environment\anaconda3\Library\mingw-w64\bin

```

### 解决命令行无法启动conda

解决“无法加载文件 \WindowsPowerShell\profile.ps1，因为在此系统上禁止运行脚本”

在VScode使用anaconda时，提示

```text
. : 无法加载文件 C:\Users\47370\Documents\WindowsPowerShell\profile.ps1，因为在此系统上禁止运行脚本。有关详
细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
```
想了解计算机上的现用执行策略，打开 PowerShell 然后输入：
```text
>> get-executionpolicy
Restricted
```
更改执行策略，以管理员身份打开 PowerShell 输入：
```text
>> set-executionpolicy remotesigned
```

### 软件源

```shell
软件包源
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/

conda源
conda config --add channels http://mirrors.aliyun.com/anaconda/pkgs/main
conda config --add channels http://mirrors.aliyun.com/anaconda/pkgs/r
conda config --add channels http://mirrors.aliyun.com/anaconda/pkgs/msys2

conda config --set show_channel_urls yes



conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge 
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
conda config --add channels http://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
conda config --set show_channel_urls yes
```

查看cuda支持版本
nvidia-smi

torch.cuda.is_available()

### conda环境管理

```
激活paddle_env环境
conda activate paddle_env

查看当前python的位置
where python

退出环境
conda deactivate env_name

切换环境
conda activate my_test

查看环境
conda env list

在当前环境里安装ipykernel

conda install ipykernel
```

pip install --upgrade pip -i http://mirrors.aliyun.com/pypi/simple/

### 删除conda

```text
方法一：
# 第一步：首先退出环境
conda deactivate
 
# 第二步：查看虚拟环境列表，此时出现列表的同时还会显示其所在路径
conda env list
 
# 第三步：删除环境
conda env remove -p 要删除的虚拟环境路径
conda env remove -p /home/kuucoss/anaconda3/envs/tfpy36   #我的例子

方法二：

# 第一步：首先退出环境
conda deactivate
 
# 第二步：删除环境
conda remove -n  需要删除的环境名 --all
```

### 验证cuda和cudnn

```
在下面路径打开终端
C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.8\extras\demo_suite

验证，显示pass即可用
.\bandwidthTest.exe

.\deviceQuery.exe
```

### tensorrt

[TensorRT SDK | NVIDIA Developer](https://developer.nvidia.com/tensorrt)

```
下载完成后，解压

将 TensorRT-7.2.2.3\include中头文件 copy 到C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\include

将TensorRT-7.2.2.3\lib 中所有lib文件 copy 到C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\lib\x64

将TensorRT-7.2.2.3\lib 中所有dll文件copy 到C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\bin
```

### miniconda

安装教程
下载软件：wget后面的地址就是上一步复制的下载链接
```
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py38_4.8.3-Linux-x86_64.sh --no-check-certificate
```

安装软件：安装过程中根据提示输入enter或yes
bash Miniconda3-py38_4.8.3-Linux-x86_64.sh

验证安装：重启终端（必须），运行下方命令，显示版本号则安装成功
conda -V

换清华源
在用户目录（/home/xxx）下新建.condarc文件
touch .condarc

用vim打开.condarc文件，写入下方内容保存后退出
```
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```
执行以下命令清除索引缓存，保证用的是镜像站提供的索引
conda clean -i

配置完毕，执行以下命令查看是否已经换源，可以看到已经更换为清华源了
conda config --show
