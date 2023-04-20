- 创建新的conda环境

在命令行输入以下命令，创建名为paddle_env的环境
此处为加速下载，使用清华源
conda create --name paddle_env python=3.8 --channel https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/  

激活刚创建的conda环境，在命令行中输入以下命令：

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