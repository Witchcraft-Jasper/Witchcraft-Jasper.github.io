+++
title = '使用MacOS为本地及服务器配置jupyter及zsh'
date = 2024-04-03T19:40:20+08:00
draft = false
tags = ["环境配置", "Linux", "Mac", "Jupyter"]
+++
# 如何配置jupyter及zsh

> 转载自本人实习期间于公司内部撰写的环境配置教程，已对其中内容进行了脱敏处理

## Why this？

jupyter lab是一个基于Web的交互式开发环境，可以让用户以可视化的方式编辑、运行和分享代码。用户可以分步执行代码，可以进行数据分析、科学计算、机器学习等各种任务。尤其对于pyspark等数据处理任务来说，使用jupyter可以显示每一个过程的运行结果，便于加深对数据处理的理解和代码的学习。

zsh是bash的替代品，两者功能几乎相同，但通过oh-my-zsh这样一个配置管理工具，zsh可以安装各种插件、主题，用户能够根据自己的需求和偏好进行个性化定制，提升开发效率。

## 配置jupyter

首先进入服务器

```shell
ssh your_server_address
```

### 安装anaconda

在清华镜像源下载安装脚本https://repo.anaconda.com/archive/  请根据服务器架构选择对应版本

在服务器自己的环境中执行安装脚本，一路yes之后默认会安装在路径 ~/anaconda3 下，尝试执行conda --version即可检测是否已安装成功。如果找不到命令尝试重进或source ~/.bashrc

### 安装jupyter

建立子环境安装jupyter相关常用包，将-n后虚拟环境的名称修改为自己起的环境名

```shell
conda create -n py3.7_env python=3.7 jupyterlab numpy pandas scipy
```

进入~/.bashrc在最后添加以下内容，其作用是设置安装了jupyter的环境默认启动并配置hdfs常用命令。将第一行环境名改为之前自己创建的环境名称，HHOME及sp改为自己的名字

```shell
conda activate py3.7_env

export HADOOP_USER_NAME=xxx
export HADOOP_USER_PASSWORD=xxx
export HHOME='/user/bigdata-dp/your_name'
export LD_LIBRARY_PATH=/lib:/usr/lib:/usr/local/lib
alias hls="hadoop fs -ls"
alias hget="hadoop fs -get"
alias hput="hadoop fs -put"
alias hcat="hadoop fs -cat"
alias http="python -m http.server"
alias hdu="hadoop fs -du -h"
```

### 设置启动脚本

进入路径/home/odin/your_name/anaconda3/envs，请将其中名字替换为自己的名字

执行 tar -czvf my_py37.tar ./py3.7_env 将conda环境打包，请将环境名、tar包名替换为自己的名字

将打好的tar包上传到hdfs中自己指定的目录下

vim start_jupyter.sh并在其中添加以下内容，注意需要将以下内容作替换：

1. PYSPARK_DRIVER_PYTHON_OPTS中ip是服务器ip，端口选一个不被占用的就行
2. pyspark --name改为自己起的名字
3. --archives下路径改为之前上传到hdfs的tar包的路径
4. spark.yarn.appMasterEnv.PYSPARK_PYTHON改为自己名字路径

```shell
export HADOOP_USER_NAME=xxx
export HADOOP_USER_PASSWORD=xxx
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='lab --port=8066 --ip=your_ip_address'
export SPARK_HOME=/usr/local/spark-current

pyspark --name your_name_data_process \
        --queue root.your_queue_name \
        --driver-memory 8g --executor-memory 13g \
        --executor-cores 3 --num-executors 100 \
	--conf spark.task.cpus=3 \
	--conf "spark.driver.extraJavaOptions=-Dcom.github.fommil.netlib.BLAS=com.github.fommil.netlib.F2jBLAS" \
        --conf "spark.executor.extraJavaOptions=-Dcom.github.fommil.netlib.BLAS=com.github.fommil.netlib.F2jBLAS"\
        --conf spark.default.parallelism=1300 \
        --conf spark.sql.shuffle.partitions=1300 \
	--conf spark.sql.broadcastTimeout=3600 \
        --conf spark.shuffle.memoryFraction=0.6 \
        --conf spark.yarn.executor.memoryoverhead=13000M \
        --conf spark.yarn.appMasterEnv.PYSPARK_PYTHON=./mypython/py3.7_env/bin/python \
        --conf spark.port.maxRetries=300 \
        --conf spark.yarn.priority=100 \
        --archives hdfs://DClusterNmg3:8020/user/your_path/your_name/my_py37.tar#mypython \
```

执行nohup sh start_jupyter.sh >jupyter.log&即可启动jupyter服务，在jupyter.log中可以找到访问链接，复制到浏览器打开即可使用

(可选)安装暗色模式jupyter lab界面，看自己需求安装，安装好之后在settings->theme中可以更换

```shell
pip install jupyterlab_darkside_ui -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 配置zsh

考虑到安装oh-my-zsh需要访问github链接，而服务器往往访问速度较慢，因此建议先在本地安装再将其复制到服务器上，也省去了重复安装插件的时间

### 本地安装oh-my-zsh(MacOS)

mac本地默认安装有zsh，因此只需要安装oh-my-zsh即可。执行

```shell
sh -c "$(curl -fsSL https://install.ohmyz.sh/)" 
```

或

```shell
sh -c "$(wget -O- https://install.ohmyz.sh/)"
```

安装好之后shell自动会从bash切换为zsh界面。

按需安装以下两个插件：

1. zsh-autosuggestions（命令联想）：记录以前输入过的命令，下次输入的时候自动推测联想

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

2. zsh-syntax-highlighting（语法高亮）：根据是否可以执行该shell命令显示不同颜色

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting 
```

安装好以上两个插件后编辑~/.zshrc，在其中plugins部分添加你已经安装好的插件名

```shell
plugins=(
    # other plugins...
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

接着执行source ~/.zshrc即可

### 服务器安装zsh(Linux)

与MacOS不同，Linux环境下终端没有zsh，因此需要先安装zsh，再进行oh-my-zsh的配置

考虑到Linux常为远程服务器，可能网速较差。因此在这里选择使用sftp命令将本地已安装好的zsh直接复制到服务器上

```shell
put -r ~/.oh-my-zsh
put ~/.zshrc
```

在服务器上自己环境下执行以下命令下载zsh安装包

```shell
wget -O zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download --no-check-certificate
```

如果因为网络问题无法下载，就在本地执行以上命令并用sftp传到服务器上

执行命令解压安装包 tar -xvf zsh.tar.xz ，会生成一个zsh-5.9的目录

进入zsh-5.9目录，顺序执行以下三条命令将zsh进行编译和安装。prefix指定要安装的目录，可以自己建个文件夹


```shell
cd zsh-5.9 
./configure -prefix=xxx
make -j all
make install
```

安装完成后打开~/.bashrc添加以下内容在conda activate之前，PATH请改为上一步安装zsh路径下的bin，保存退出后**先不要source**

```shell
export PATH=$PATH:/home/odin/your_name/my_zsh/bin
exec zsh
```

### 服务器安装oh-my-zsh(Linux)

cd ~ 到主目录后，用sftp get将之前上传的.oh-my-zsh文件夹及.zshrc获取

vim ~/.zshrc在最后添加以下两段内容。下方的内容只是示例，请从自己的.bashrc中找到对应内容进行复制

```shell
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/odin/your_name/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/odin/your_name_i/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/odin/your_name_i/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/odin/your_name/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
```

```shell
conda activate py3.7_env

export HADOOP_USER_NAME=xxx
export HADOOP_USER_PASSWORD=xxxxx
export HHOME='/user/bigdata-dp/your_name'
export LD_LIBRARY_PATH=/lib:/usr/lib:/usr/local/lib
alias hls="hadoop fs -ls"
alias hget="hadoop fs -get"
alias hput="hadoop fs -put"
alias hcat="hadoop fs -cat"
alias http="python -m http.server"
alias hdu="hadoop fs -du -h"
```

在.zshrc最后再添加以下内容防止zsh颜色显示出现问题

```shell
export TERM=xterm-256color
```

保存.zshrc退出后执行 source ~/.bashrc即可完成安装

(可选)更换主题：通过修改~/.zshrc中ZSH_THEME="robbyrussell"可以替换不同的主题，参考以下网页中的内容可以进行替换: 
https://github.com/ohmyzsh/ohmyzsh/wiki/Themes

(可选)更换字体，自定义zsh主题: 
https://juejin.cn/post/6844904178075058189#heading-32

