+++
title = 'Configuring Jupyter and zsh for Local and Server Environments on MacOS'
date = 2024-04-02T22:56:20+08:00
draft = true
+++
# How to Configure Jupyter and zsh

> This page is translated by GPT-4

## Why this？

Jupyter Lab is a web-based interactive development environment that allows users to edit, run, and share code visually. Users can execute code step by step, which is suitable for tasks such as data analysis, scientific computing, machine learning, etc. Especially for data processing tasks like PySpark, using Jupyter displays the results of each process, facilitating a deeper understanding of data processing and code learning.

zsh is an alternative to bash, offering almost the same functionality. However, with a configuration management tool like oh-my-zsh, zsh can install various plugins and themes, allowing users to customize it according to their needs and preferences, thereby enhancing development efficiency.

## Configuring Jupyter

First, enter your own environment on the server

```shell
ssh your_server_address
```

### Installing Anaconda

Download the installation script from Tsinghua mirror source https://repo.anaconda.com/archive/. Please choose the version corresponding to the server architecture.

Execute the installation script in your own environment on the server. After proceeding with yes, it will by default install in the path ~/anaconda3. Try running conda --version to check if it's successfully installed. If the command is not found, try re-entering or source ~/.bashrc.

### Installing Jupyter

Create a sub-environment to install common Jupyter packages. Change the name after -n to your own environment name.

```shell
conda create -n py3.7_env python=3.7 jupyterlab numpy pandas scipy
```

Add the following content at the end of ~/.bashrc, which sets the environment with Jupyter installed as the default start-up and configures common hdfs commands. Change the first line's environment name to the one you previously created, and change HHOME and sp to your name.

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

### Setting Up Startup Script

Navigate to the path /home/odin/your_name/anaconda3/envs. Please replace the name with your own.

Execute tar -czvf my_py37.tar ./py3.7_env to package the conda environment. Please replace the environment name and tar package name with your own.

Upload the created tar package to the designated directory in hdfs.

Edit start_jupyter.sh and add the following content, noting that you need to replace the following:

1. In PYSPARK_DRIVER_PYTHON_OPTS, the IP is the server IP, and you can choose any port that is not occupied.
2. Change pyspark --name to the name you have chosen.
3. Change the path under --archives to the path of the tar package previously uploaded to hdfs.
4. Change spark.yarn.appMasterEnv.PYSPARK_PYTHON to your name's path.

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

Execute nohup sh start_jupyter.sh >jupyter.log& to start the jupyter service. You can find the access link in jupyter.log, copy it to the browser to use.

(Optional) Install the dark mode interface for jupyter lab, install it according to your needs, and you can switch in settings->theme after installation.

```shell
pip install jupyterlab_darkside_ui -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## Configuring zsh

Considering accessing the github link to install oh-my-zsh is often slow on servers, it is recommended to first install it locally and then copy it to the server, saving time on reinstalling plugins.

### Local Installation of oh-my-zsh (MacOS)

Mac comes with zsh installed by default, so you only need to install oh-my-zsh. Execute

```shell
sh -c "$(curl -fsSL https://install.ohmyz.sh/)" 
```

or

```shell
sh -c "$(wget -O- https://install.ohmyz.sh/)"
```

After installation, the shell will automatically switch from bash to zsh.

Install the following two plugins as needed:

1. zsh-autosuggestions: records commands entered in the past and automatically predicts suggestions when entering commands again.

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

2. zsh-syntax-highlighting: displays different colors based on whether the shell command can be executed.

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting 
```

After installing the above two plugins, edit ~/.zshrc and add the names of the plugins you have installed to the plugins section.

```shell
plugins=(
    # other plugins...
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

Then execute source ~/.zshrc.

### Server Installation of zsh (Linux)

Unlike MacOS, Linux terminals do not come with zsh, so you need to install zsh first before configuring oh-my-zsh.

Considering Linux often serves as a remote server with potentially slow internet, here we choose to use the sftp command to directly copy the locally installed zsh to the server.

```shell
put -r ~/.oh-my-zsh
put ~/.zshrc
```

On your server environment, execute the following command to download the zsh installation package.

```shell
wget -O zsh.tar.xz https://sourceforge.net/projects/zsh/files/latest/download --no-check-certificate
```

If downloading is not possible due to network issues, execute the command locally and use sftp to transfer it to the server.

Execute tar -xvf zsh.tar.xz to extract the installation package, which will create a directory named zsh-5.9.

Enter the zsh-5.9 directory, and execute the following commands in sequence to compile and install zsh. The prefix specifies the installation directory, you can create a folder for it


```shell
cd zsh-5.9 
./configure -prefix=xxx
make -j all
make install
```

After installation, add the following content to ~/.bashrc before conda activate. Change PATH to the bin directory under the installation path of zsh from the previous step, save, and exit, **but do not source yet.**

```shell
export PATH=$PATH:/home/odin/your_name/my_zsh/bin
exec zsh
```

### Server Installation of oh-my-zsh (Linux)

After navigating to the home directory, use sftp get to retrieve the previously uploaded .oh-my-zsh folder and .zshrc.

Edit ~/.zshrc and add the following sections at the end. The content below is just an example, please copy the corresponding content from your own .bashrc.

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

Add the following to ~/.zshrc to prevent color display issues in zsh.

```shell
export TERM=xterm-256color
```

Save and exit .zshrc, then execute source ~/.bashrc to complete the installation.

(Optional) Change the theme: You can replace different themes by modifying ZSH_THEME="robbyrussell" in ~/.zshrc. Refer to the content on the following webpage for replacement: https://github.com/ohmyzsh/ohmyzsh/wiki/Themes

(Optional) Change fonts, customize zsh theme: https://juejin.cn/post/6844904178075058189#heading-32

