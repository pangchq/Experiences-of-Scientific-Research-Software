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