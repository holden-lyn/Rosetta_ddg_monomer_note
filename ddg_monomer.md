# Rosetta中ddg_monomer应用的使用教程。

ddg_monomer是rosetta中计算蛋白质结构突变前后吉布斯自由能的差值的应用，是计算机辅助设计的常用手段。一般来说，ddg_monomer计算出来的ddg小于0，则表示ddg_monomer预测蛋白质突变之后的结构具备更高的热稳定性。

调用ddg_monomer其实和调用其他应用一样，都是在rosetta文件夹/main/source/bin的路径中使用``/path/to/ddg_monomer.<mpi/default/*>.linuxgccrelease``，需要调用多核同步进行运算的话，在指令面前加上``mpirun -np *``，*为核数，即同时进行的运算数，根据手头拥有的计算资源进行决定。  

建议全程关注指令中的文件名，路径等是否正确。  
  
ddg_monomer有根据蛋白质结构分辨率高低两种不同的计算选项，此处介绍测试过并成功跑通的高分辨率方法。调用ddg_monomer之前，还需要先准备三个不同文件：  
（1）经过清理（可用rosetta内自带python脚本清理）的蛋白质结构文件.pdb；  
（2）距离限制文件.cst；  
（3）突变信息文件.mutfile。下面将逐个介绍以上文件的准备方式。

我的习惯是创建一个文件夹来装某一次ddg_monomer运行所需的全部文件，以及运算之后的输出文件。

先使用rosetta内置python脚本来运行clean.py清理蛋白质序列。

```
python <Rosetta_path>/tools/protein_tools/scripts/clean_pdb.py <protein.pdb> <protein_chain_No.>
```

此处:  
- "Rosetta_path"指的是Rosetta的路径  
- "protein.pdb"指的是输入的蛋白质结构文件.pdb，如果当前路径中没有，会直接从蛋白质结构数据rcsb.org下载（本次测试使用的就是脚本帮忙下载的蛋白质结构.pdb文件，本次测试时使用从uniprot下载的pdb文件进行清理时，在生成距离限制文件.cst的时候发生错误，故推荐让脚本自动从rcsb.org获取）  
- "protein_chain_No."指的是蛋白质链的序号，本教程中使用的是D-allulose 3-epimarase，即阿洛酮糖3差向异构酶，由6个相同的链组成，这里选取A链进行清理:  

```
python /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/tools/protein_tools/scripts/clean_pdb.py 3ct7.pdb A
```

这时候会得到"3CT7_A.pdb"，"3CT7_A.fasta"文件。  
  
进行能量最小化
```
mpirun -np 6 <rosetta_path>/main/source/bin/minimize_with_cst.mpi.linuxgccrelease -in:file:s 3CT7_A.pdb -in:file:fullaton -ignore_unrecognized_res -fa_max_dis 9.0 -database <rosetta_path>/main/database -ddg::harmonic_ca_tether 0.5 -ddg::constraint_weight 1.0 -ddg::out_pdb_prefix min_cst_0.5 -ddg::sc_min_only > mincst_3ct7.log
```
  
这里得到的"mincs_3ct7.log"文件，是能量最小化过程生成的log文件，可以在文本编辑器里面检查一下是否生成了后续.cst文件需要的关键信息，例如：  

apps.public.ddg.minimize_with_cst: c-alpha constraints added to residues 2 and 1 dist 3.82502 and tol 0.5  
apps.public.ddg.minimize_with_cst: c-alpha constraints added to residues 3 and 1 dist 6.72432 and tol 0.5  
apps.public.ddg.minimize_with_cst: c-alpha constraints added to residues 3 and 2 dist 3.81912 and tol 0.5  
apps.public.ddg.minimize_with_cst: c-alpha constraints added to residues 4 and 2 dist 6.88404 and tol 0.5  
...  
  
确认安装tcsh，可以运行shell脚本
```
tcsh --version
```
如果没有安装，用
```
apt install tcsh
```
  
使用自带的脚本抓取信息，生成距离限制文件.cst。  
需要注意的是自带的脚本中的抓取命令有点过时，无法准确抓取log文件中的信息，这次测试将脚本编辑成如下，以替代原脚本文件：
```
grep ^apps.public.ddg.minimize_with_cst $1 | awk '{print "AtomPair CA "$7" CA "$9" HARMONIC "$11" "$14}'
```
现在再运行脚本，甚至可以直接把新编辑的脚本复制到当前的工作路径。
```
tcsh <rosetta_path>/main/source/src/apps/public/ddg/convert_to_cst_file.sh mincst_3ct7.log >input.cst
```
如果脚本就在当前路径
```
tcsh ./convert_to_cst_file.sh mincst_3ct7.log >input.cst
```
会得到名为"input.cst"的距离限制文件，取决于指令中指定的生成文件名。  
  
最后再准备一下突变信息文件.mutfile，可以是txt文件格式，文件内的突变信息格式如下：  
  
***注意，在之前用clean.py清理蛋白质的时候，蛋白质序列有可能会被重新排列，建议用蛋白质结构可视化软件，如UCSF Chimera，PyMol等，确认想要突变的蛋白质在清理后的.pdb文件中的序列。（此案例3CT7_A.pdb）***  
  
本次突变一个位点，是第28位由氨基酸Y突变成氨基酸A：  
  
total 1  
1  
Y 28 A  
  
用文本编辑器编辑好之后，保存为.mutfile格式即可。  
  
关于mutfile文件更复杂的编写格式，详情查阅官方文档 ``https://new.rosettacommons.org/docs/latest/rosetta_basics/file_types/resfiles``，同样是域名后续可能会变，根据关键词从官网主页面开始找吧。  

通过上述步骤，在当前的工作文件夹中，应该已经有了运行ddg_monomer应用所需的三个文件：(1)3CT7.pdb, (2)input.cst (3) 3ct7_28YtoA.mutfile （这里命名不太简洁，对于一个突变位点可以改用类似"3ct7_Y28A.mutfile"这样的形式）。  
  
还需准备最后一个文件flags文件，flags文件是用于指定ddg_monomer的运行参数的，用文本编辑器编辑，同样是可以保存为txt，如果已经有.txt后缀，输入运行指令的时候，记得带上这个后缀。  
flags文件内容如下，这里为避免混淆说明flags文件***并不是***后缀为.flags，而是在输入ddg_monomer运行指令是"@"后面跟的文件名，因为其功能性（指定了运行中的各种flags，如下面的“-ddg:weight_file standard_plus_score12 ”）而被称作flag。
```
-in:file:s 3CT7_A.pdb
-ddg:mut_file 3ct7_28YtoA.mutfile
#-ddg:weight_file soft_rep_design
-ddg:weight_file standard_plus_score12 #请注意这里我在当前工作文件夹加入了rosetta包自备的打分文件"standard_plus_score12.wts"，在实际应用时往往使用上一行的weight_file设置
-ignore_unrecognized_res
#-database /mnt/4T_sdb/LHL/test/rosetta_src_2021.16.61629_bundle/main/database/
-fa_max_dis 9.0
#-ddg:minimization_scorefunction talaris2013
-ddg:iterations 2
-ddg:dump_pdbs true
-ignore_unrecognized_res
-ddg:local_opt_only true
-ddg:min_cst true
-constraints:cst_file input.cst
-ddg:suppress_checkpointing true
-in:file:fullatom 
-ddg:mean false
-ddg:min true
-ddg:sc_min_only false
-ddg:ramp_repulsive true
-unmute core.optimization.LineMinimizer
-ddg:output_silent true

-out:level 500
```
  
保存为.txt，万事俱备只差一行指令。






