## 扩散能垒  
**VASP CINEB**  
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