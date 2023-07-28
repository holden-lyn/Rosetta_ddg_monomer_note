# Rosetta中ddg_monomer应用的使用教程。

ddg_monomer是rosetta中计算蛋白质结构突变前后吉布斯自由能的差值的应用，是计算机辅助设计的常用手段。一般来说，ddg_monomer计算出来的ddg小于0，则表示ddg_monomer预测蛋白质突变之后的结构具备更高的热稳定性。

调用ddg_monomer其实和调用其他应用一样，都是在rosetta文件夹/main/source/bin的路径中使用``/path/to/ddg_monomer.<mpi/default/*>.linuxgccrelease``，需要调用多核同步进行运算的话，在指令面前加上``mpirun -np *``，*为核数，即同时进行的运算数，根据手头拥有的计算资源进行决定。

ddg_monomer有根据蛋白质结构分辨率高低两种不同的计算选项，此处介绍测试过并成功跑通的高分辨率方法。调用ddg_monomer之前，还需要先准备三个不同文件：
\r（1）经过清理（可用rosetta内自带python脚本清理）的蛋白质结构文件.pdb；
\r（2）距离限制文件.cst；
\r（3）突变信息文件.mutfile。下面将逐个介绍以上文件的准备方式。

我的习惯是创建一个文件夹来装某一次ddg_monomer运行所需的全部文件，以及运算之后的输出文件。

先使用rosetta内置python脚本来运行clean.py清理蛋白质序列。

```
python <Rosetta_path>/tools/protein_tools/scripts/clean_pdb.py <protein.pdb> <protein_chain_No.>
```

此处:
"Rosetta_path"指的是Rosetta的路径
"protein.pdb"指的是输入的蛋白质结构文件.pdb，如果当前路径中没有，会直接从蛋白质结构数据rcsb.org下载（本次测试使用的就是脚本帮忙下载的蛋白质结构.pdb文件，本次测试时使用从uniprot下载的pdb文件进行清理时，在生成距离限制文件.cst的时候发生错误，故推荐让脚本自动从rcsb.org获取）
"protein_chain_No."指的是蛋白质链的序号，本教程中使用的是D-allulose 3-epimarase，即阿洛酮糖3差向异构酶，由6个相同的链组成，这里选取A链进行清理:

```
python /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/tools/protein_tools/scripts/clean_pdb.py 3ct7.pdb A
```

