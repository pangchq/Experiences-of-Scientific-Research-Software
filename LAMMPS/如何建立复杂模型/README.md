## 如何建立复杂模型  
**结合PACKMOL与moltemplate**  
下面是一个水液滴蒸发模型的例子  
1. 根据原子坐标、键长、键角等参数手动准备TIP4P.pdb、N2.pdb  
TIP4P.pdb  
```
COMPND    PDB File
COMPND   1Created by VESTA
HETATM    1  O           1       0.000   0.000   0.000  1.00  0.00           O
HETATM    2  H           1      0.9572   0.000   0.000  1.00  0.00           H
HETATM    3  H           1    -0.23999 0.92663   0.000  1.00  0.00           H
END
```
N2.pdb  
```
COMPND    PDB File
COMPND   1Created by VESTA
HETATM    1  N           1       0.000   0.000   0.000  1.00  0.00           N
HETATM    2  N           1       0.000   1.100   0.000  1.00  0.00           N
END

```
2. 根据PACKMOL语法准备inp文件, 运行生成pdb文件,该pdb文件包含所有原子坐标  
model.inp  
```
tolerance 2.0
output model.pdb
filetype pdb

structure TIP4P.pdb
  number 8000
  inside sphere 120. 120. 120. 50.
end structure 
 
structure N2.pdb
  number 27000
  inside cube 0. 0. 0. 240.
  outside sphere 120. 120. 120. 50.
end structure

```
运行PACKMOL  
```
packmol < model.inp
```
生成了model.pdb文件  
3. 根据moltemplate语法以及坐标、键长、键角等参数手动准备TIP4P.lt、N2.lt, lt文件包含完整的拓步信息  
TIP4P.lt
```
# TIP4P/2005 with a long-range Coulombic solver

TIP4P {

  # AtomID MoleculeID AtomType charge    X       Y       Z

  write("Data Atoms") {
    $atom:O   $mol:w  @atom:O  -1.1128  0.00000  0.00000  0.00000
    $atom:H1  $mol:w  @atom:H   0.5564  0.95720  0.00000  0.00000
    $atom:H2  $mol:w  @atom:H   0.5564 -0.23999  0.92663  0.00000
  }

  write_once("Data Masses") {
    @atom:O 15.9994
    @atom:H 1.008
  }

  write("Data Bonds") {
    $bond:OH1 @bond:OH $atom:O $atom:H1
    $bond:OH2 @bond:OH $atom:O $atom:H2
  }

  write("Data Angles") {
    $angle:HOH @angle:HOH $atom:H1 $atom:O $atom:H2
  }

  write_once("In Settings") {
    bond_coeff   @bond:OH         527.0   0.9572
    angle_coeff  @angle:HOH       37.95   104.52
    pair_coeff   @atom:O @atom:O  0.1852  3.1589 
    pair_coeff   @atom:H @atom:H  0.0     0.0
    group water type  @atom:O  @atom:H
  }

  write_once("In Init") {
    units        real
    atom_style   full
    #pair_style   lj/cut/tip4p/cut 
    bond_style   harmonic
    angle_style  harmonic
  }

 }

```
N2.lt
```
# N2

N2 {

  # AtomID  MolID  AtomType  charge   X       Y         Z
  write("Data Atoms") {
    $atom:N1 $mol @atom:N    0.0    0.00000  0.00000   0.000000
    $atom:N2 $mol @atom:N    0.0    1.10000  0.00000   0.000000
  }

  write_once("Data Masses") {
    @atom:N 14.007
  }

  write("Data Bonds") {
    $bond:N1N2 @bond:NN $atom:N1 $atom:N2
  }

  write_once("In Settings") {
    bond_coeff   @bond:NN         50.0   1.1
    pair_coeff   @atom:N @atom:N  0.101  3.341
    group N2 type  @atom:N
  }

}

```
4. 根据moltemplate语法准备model.lt文件  
```
import "TIP4P.lt"
import "N2.lt"

water  = new TIP4P[8000]

nitrogen = new N2[27000]

# Periodic boundary conditions:
write_once("Data Boundary") {
   0.0  240.0000  xlo xhi
   0.0  240.0000  ylo yhi
   0.0  240.0000  zlo zhi
}

```
5. 运行moltemplate生成LAMMPS的data文件  
```
moltemplate.sh -pdb model.pdb model.lt
```