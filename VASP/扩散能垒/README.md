## 扩散能垒  
**VASP CI-NEB**  
1. IS、FS结构优化  
IS文件夹
```
CHG  CHGCAR  CONTCAR  DOSCAR  EIGENVAL  IBZKPT  INCAR  IS.xsd  KPOINTS  OSZICAR  OUTCAR  output.log  PCDAT  POSCAR  POTCAR  REPORT  run_vasp  tmp  vasprun.xml  WAVECAR  XDATCAR
```
FS文件夹
```
CHG  CHGCAR  CONTCAR  DOSCAR  EIGENVAL  IBZKPT  INCAR  IS.xsd  KPOINTS  OSZICAR  OUTCAR  output.log  PCDAT  POSCAR  POTCAR  REPORT  run_vasp  tmp  vasprun.xml  WAVECAR  XDATCAR
```
2. mkdir TS && cd TS
生成中间结构文件有两种方法, 推荐非线性方法:  
* 线性  
```
nebmake.pl ../IS/CONTCAR ../FS/CONTCAR N
```
* 非线性  
```
python idpp.py N ../IS/CONTCAR ../FS/CONTCAR
```
3. 准备过渡态输入文件INCAR、POTCAR、KPOINTS和提交脚本run_vasp, 放置在TS路径下  
INCAR有几个关键词要注意  
```
EDIFF    = 1E-07               # 这个参数非常关键！过渡态对力计算精度要求极高，更精准的电子步收敛会有更精准的力，可以加速收敛（注：粗收敛可以适当放宽到1E-5）
EDIFFG   = -3E-02              # 过渡态可以适当放宽结构优化的收敛精度到-0.03，对于非常复杂难以收敛的体系可以放宽到-0.05
IBRION   = 3; POTIM    = 0     # 这是VTST识别并启动VTST优化算法的标志
IOPT     = 1                   # 优化算法: 1=LBFGS, 2= CG
ICHAIN   = 0                   # 开启NEB
LCLIME   = T                   # CI-NEB
IMAGES   = 4                   # number of IMAGES
SPRING   = -5                  # 弹簧力常数，用-5默认值即可
```
4. 在TS路径下提交任务, 可以用nebef.pl来查看计算状态  
运行nebef.pl会得到类似如下内容, 第一列对应00 01 02 03 04 05文件夹, 第二列是最大原子受力, 第三列是能量, 第四列是相对初态的能量。当所有插点的最大原子受力都<|EDIFFG|时，计算收敛。若中间插了多个点，则所有点都要<|EDIFFG|才能收敛。  
```
   0         0.009821      -284.231300         0.000000
   1         0.259777      -283.056100         1.175200
   2         0.292155      -283.062100         1.169200
   3         0.271117      -283.052300         1.179000
   4         0.260467      -283.062300         1.169000
   5         0.009953      -284.231300         0.000000
```
5. 计算完毕后, nebresults.pl总结结果, 输出如下  
```

```
nebresult.pl命令执行了nebbarrier.pl, nebspline.pl, nebef.pl, nebmovie.pl, nebjmovie.pl, nebconverge.pl等, 在TS路径下生成了很多文件, 其中mep.eps是以dist.pl距离为横坐标，能量为纵坐标画出的能势垒图, 如下图所示:  
这里给个图片路径  
生成的vaspgr文件夹内是各个插点结构的收敛图, 如下图所示:  
这里给个图片路径  