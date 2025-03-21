---
title: Anaconda
tags:
  - 
date created: 2023-03-21
date modified: 2024-12-27
---

# GPU性能对比

![](Paper/2024年/attachments/Few-Shot%20Medical%20Image%20Segmentation%20via%20Generating%20Multiple%20Representative%20Descriptors-6.png)
![](Paper/2024年/attachments/Few-Shot%20Medical%20Image%20Segmentation%20via%20Generating%20Multiple%20Representative%20Descriptors-8.png)

[python多个虚拟环境需要共用一个包，怎么避免重复安装_pytorch是需要在不同的虚拟环境下重复安装嘛-CSDN博客](https://blog.csdn.net/Sallee001/article/details/127799088)

# 共享软件包

![](环境配置/attachments/Pasted%20image%2020241129152334.png)

```
$dirList =
    "functorch",
    "torch",
    "torchgen",
    "torchaudio",
    "torchvision",
    "doclayout_yolo",
    "pdf2zh",
    "thop"

$linkPath = "E:\Environment\anaconda3\envs\CATNet\Lib\site-packages\"		
#这里写新环境的site-packages路径
$RealPath = "E:\Environment\anaconda3\envs\commontorch\Lib\site-packages\"	
#这里写原环境的site-packages路径

for($i = 0; $i -lt $dirList.Count; $i++)
{
  $linkFullPath = $linkPath+$dirList[$i]
  $realFullPath = $realPath+$dirList[$i]
  New-Item -ItemType SymbolicLink -Path $linkFullPath -Target $realFullPath
}

```

### 复制环境

```
conda create --name gpt python=3.10 -y

conda create --name commontorch python=3.10 -y
pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu118
pip install opencv sacred SimpleITK jupyter
将 `torch=2.0.1` 标记为禁止更新的软件包。
conda config --add pinned_packages pytorch=2.0.1
conda config --add pinned_packages torchvision=0.15.2
导出备份
pip freeze > commontorch.txt
移除
conda remove -n  CATNet --all
克隆环境软链接
conda create --name CATNet --clone commontorch


bzip2-1.0.8-h2bbff1b_6  
ca-certificates-2024.9.24-haa95532_0  
json5-0.8.5-py_0  
libffi-3.4.4-hd77b12b_1  
openssl-3.0.15-h827c3e9_0  
pip-24.2-py310haa95532_0  
python-3.10.15-h4607a30_1  
setuptools-75.1.0-py310haa95532_0  
sqlite-3.45.3-h2bbff1b_0  
tk-8.6.14-h0416ee5_0  
tzdata-2024b-h04d1e81_0  
vc-14.40-h2eaa2aa_1  
vs2015_runtime-14.40.33807-h98bb1dd_1  
wheel-0.44.0-py310haa95532_0  
xz-5.4.6-h8cc25b3_1  
zlib-1.2.13-h8cc25b3_1
```

### NABirds数据集

https://www.dropbox.com/scl/fi/yas70u9uzkeyzrmrfwcru/nabirds.tar.gz?e=1&n=13142758&oref=e&rlkey=vh0uduhckom5jyp73igjugqtr&submissionGuid=4c902ca4-854c-4057-a59c-a9144baa3e7c

### shell

```sh
conda create --name net python=3.10 -y
conda activate net
conda install cudatoolkit==11.8
pip install -r requirements.txt
pip install torch==2.0.1 torchvision==0.15.2 --index-url https://download.pytorch.org/whl/cu118
```

### CATNet

conda create --name CATNet python=3.10 -y

[download.pytorch.org/whl/torch_stable.html](https://download.pytorch.org/whl/torch_stable.html)

```
torch安装

conda create --name dsp python=3.8 -y
conda install cudatoolkit==11.8

pip install torch==1.8.1+cu111 torchvision==0.9.1+cu111 -f https://download.pytorch.org/whl/torch_stable.html

torch==2.0.1+cu118  
torchvision==0.15.2+cu118

pip install torch==2.0.1 torchvision==0.15.2 --index-url https://download.pytorch.org/whl/cu118

```

### Vim

```

pip install torch==2.1.1 torchvision==0.16.1 torchaudio==2.1.1 --index-url https://download.pytorch.org/whl/cu118

pip install -r vim/vim_requirements.txt

https://pypi.tuna.tsinghua.edu.cn/simple

pip --trusted-host pypi.tuna.tsinghua.edu.cn install torch==1.10.1+cu102 torchvision==0.11.2+cu102 torchaudio==0.10.1 -f https://download.pytorch.org/whl/torch_stable.html
```

### fps

```python
conda create --name fps python=3.10 -y

pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

pip install mss
```

# DSPNet

2cFrWIS4y9EM

### numpy版本问题

```
find . -type f -name "*.py" -exec sed -i 's/np\.float/np.float64/g' {} +
find . -type f -name "*.py" -exec sed -i 's/np\.float6432/np.float32/g' {} +

```

```
nohup bash -c 'cd data && mkdir coco && cd coco && wget http://images.cocodataset.org/zips/val2017.zip' > output.log 2>&1 &
```
pip freeze > requirements.txt

### 换行符

看起来你的脚本文件包含了Windows风格的换行符（`^M` 或 `\r`），这是导致错误的原因。你可以使用 `dos2unix` 来转换文件格式。试试下面的步骤：
1. 安装 `dos2unix`（如果还未安装）：
   ```bash
   sudo apt-get install dos2unix
   ```
2. 转换文件格式：
   ```bash
   dos2unix train_ssl_abdominal_ct.sh
   ```

### 数据集

```
wget -c https://zenodo.org/records/5903037/files/Subtask1.zip?download=1
wget -c https://zenodo.org/records/5903037/files/Subtask2.zip?download=1
wget -c https://zenodo.org/records/5903037/files/TestImage.zip?download=1
```

# 环境

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

```
# CUDA 11.8
pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu118
# CPU only
pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cpu
```

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


import torch
print(torch.cuda.is_available())  # 检查GPU是否可用
print(torch.cuda.get_device_name(0))  # 输出GPU设备的名称
print(torch.version.cuda)  # 查看使用的CUDA版本

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
