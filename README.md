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

**按照知乎Rosy Wu的提示，不要用edge浏览器下载安装包，会自动改为gz扩展名，导致解压出现问题。

