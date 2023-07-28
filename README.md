# Rosetta_ddg_monomer_tutorial

Rosetta安装流程以及ddg_monomer应用的使用说明


The tutorial serves as a worklog for installation of Rosetta to remote server and using the ddg_monomer application. 

检查服务器上的Linux，buntu以及gcc
```
(base) LHL 09:02:21 /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/ddg_monomer_test/3ct7

$ cat /proc/version

Linux version 5.15.0-76-generic (buildd@lcy02-amd64-019) (gcc (Ubuntu 9.4.0-1ubuntu1\~20.04.1) 9.4.0, GNU ld (G2.34) #83\~20.04.1-Ubuntu SMP Wed Jun 21 20:23:31 UTC 2023

(base) LHL 10:55:14 /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/ddg_monomer_test/3ct7

$ cat /etc/issue

Ubuntu 20.04.6 LTS \n \l

(base) LHL 10:55:22 /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/ddg_monomer_test/3ct7

$ gcc --version

gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

Rosetta包下载与license请求。

在``https://www.rosettacommons.org``寻找“license&download”，因为域名偶尔会变，所以这里只贴主页链接。

**按照知乎Rosy Wu的提示，不要用edge浏览器下载安装包，会自动改为gz扩展名，导致解压出现问题。**

向服务器传送安装包，我使用xshell，xftp两件套，xftp中“文件”，“新建”，设置端口号，用户名，密码，当然还有服务器地址。直接把下好的Rosetta压缩包拖进服务器中想要的路径里头。我这里是自己的test文件夹，路径类似``home/me/Project/test/Rosetta``。

安装依赖包
``
sudo apt-get install libboost-dev
sudo apt-get install python
sudo apt-get install zlib1g-dev
``
如果没有sudo权限可以先试试去掉sudo，或者让有权限的管理员帮忙装。

OPENMPI也得装
``
sudo apt-get install openmpi-bin openmpi-doc libopenmpi-dev
``

bash环境变量配置：
我这里是进vi去编辑添加环境变量，vi操作挺不符合平时电脑的操作习惯的，详细可以去链接里看看。这里主要用到``i``在光标位置启用编辑``esc``

``
目前，在~/.bashrc中加入：
#Rosetta
export ROSETTA=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle
export ROSETTA3_DB=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/database
export ROSETTA_BIN=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source/bin
export PATH=$PATH:$ROSETTA_BIN
export LD_LIBRARY_PATH=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source/bin:$LD_LIBRARY_PATH
export ROSETTA3=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source
export ROSETTA_TOOLS=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/tools
``


