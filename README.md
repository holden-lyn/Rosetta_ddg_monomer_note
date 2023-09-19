# Rosetta_ddg_monomer_tutorial

已经安装好Rosetta，直接看[ddg_monomer使用](ddg_monomer.md)

# 1. Rosetta安装流程以及ddg_monomer应用的使用说明


The tutorial serves as a worklog for installation of Rosetta to remote server and using the ddg_monomer application. 

检查服务器上的Linux，ubuntu以及gcc
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
我这里是进vi去编辑添加环境变量，vi操作挺不符合平时电脑的操作习惯的，详细可以去链接里看看。这里主要用到``i``在光标位置启用编辑，``esc``结束编辑回到vi的“上一层”，不保存退出：``:``后``!q``，保存退出：``:``后``wq``。建议了解一下vi界面操作再进去，免得像我第一次一样写入的时候手忙脚乱，写完又不知道编辑的东西保存了没。
vi界面操作指南``https://www.runoob.com/linux/linux-vim.html``

进入bashrc
```
vi ~/.bashrc
```

文章编辑时，在~/.bashrc中加入：
```
#Rosetta
export ROSETTA=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle
export ROSETTA3_DB=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/database
export ROSETTA_BIN=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source/bin
export PATH=$PATH:$ROSETTA_BIN
export LD_LIBRARY_PATH=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source/bin:$LD_LIBRARY_PATH
export ROSETTA3=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/source
export ROSETTA_TOOLS=/mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/tools
```

环境变量设置好之后，开始去到源码包所在路径，进行解压和编译。

去到源码所在目录，也就是压缩包在的地方，我这次：
``cd /mnt/4T_sdb/LHL/test``
解压
``tar zxvf rosetta_src_*`` 替换成和自己的压缩包一样的名字，例如我这次 ``tar zxvf rosetta_src_2021.16.61629_bundle/main/source``
使用gcc编译
``./scons.py -j 20 mode=release bin extras=mpi`` 
``-j 20``就是调用的核数，对应的``extras=mpi``会生成名为``*.mpi.linuxgccrelease``的文件，如果不加``extras=mpi``,生成的executable，也就是使用rosetta时候调用的文件，名为``*.default.linuxgccrelease``。目前我不完全理解两种方式的区别，在后续测试中发现有时候调用mpi多进程执行rosetta命令有时候会出现报错，遂同时编译了default的executables（``./scons.py -j 20 mode=release bin``）。编译所需要的时间跟调用的核数有关系，也和其他因素有关系，印象中调用4、5核我也花了几分钟分别进行.mpi和.default应用的编译。

查看有哪些功能可供使用
``ls $ROSETTA/main/source/bin/``或``ls /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/source/bin``，其实$ROSETTA在这里应该就是代替了rosetta的路径，不过环境变量的设置也可能带来其他影响，所以不要跳过环境变量的设置。可以看见同时编译了default和mpi版本的应用。
```
AbinitioRelax.default.linuxgccrelease
AbinitioRelax.linuxgccrelease
AbinitioRelax.mpi.linuxgccrelease
analyze_base_pairing.default.linuxgccrelease
analyze_base_pairing.linuxgccrelease
analyze_base_pairing.mpi.linuxgccrelease
AnchoredDesign.default.linuxgccrelease
AnchoredDesign.linuxgccrelease
AnchoredDesign.mpi.linuxgccrelease
...
```

测试，这里我用rosetta官方的教程，包含了rosetta中必须输入、输出文件的操作``https://www.rosettacommons.org/demos/latest/tutorials/input_and_output/input_and_output``,域名是有可能变的，如果改变了，请根据这里提供的关键词自己从主页开始点进去吧。





# 2. 这里提供我跟随教程指引测试rosetta功能的中文流程。

进入rosetta自带的教程文件夹``cd <path_to_Rosetta_directory>/demos/tutorials/input_and_output``

用``$ROSETTA3/bin/score_jd2.default.linuxgccrelease -in:file:s input_files/1qys.pdb``调用score_jd2应用，计算energy score（因为不够清楚energy score的中文定义所以此处保留英文）。

``ls``，会看到生成了score.sc文件，可以用``cat score.sc``看看内容。

再试试输出文件的指令
```
$ROSETTA3/bin/relax.default.linuxgccrelease -in:file:s input_files/from_rcsb/1qys.pdb -out:file:silent output_files/1qys.o @flag_input_relax
```
可以在这个路径下看到输出的文件，不过silent file不是human readable的
```
<path_to_Rosetta_directory>/demos/tutorials/input_and_output/input_files/1qys.o
```



# 3. 接下来是相当重要的ddg_monomer的应用，还是开一个新的注释文件来说明吧。

见本教程中的"[ddg_monomer.md](https://github.com/holden-lyn/Rosetta_ddg_monomer_tutorial/blob/main/ddg_monomer.md)"。








