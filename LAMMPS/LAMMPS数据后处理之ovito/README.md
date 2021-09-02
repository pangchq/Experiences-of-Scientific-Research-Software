## LAMMPS数据后处理之ovito  
**LAMMPS程序虽然自带一些分析命令，比如compute rdf、compute adf等，然而很多时候我们关心的性质或信息，LAMMPS没有相应的命令，这种情况下我们只能自己写脚本进行分析。一般情况，我们会避免“重复造轮子”，站在“巨人的肩膀上”。今天为大家介绍一款LAMMPS数据后处理软件——ovito（官网https://www.ovito.org/）。**  
ovito支持多平台，还有python API拓展。下面是一个调用ovito的python脚本, 统计蒸发的水分子数量  
```
#! bin/env python

from ovito.io import import_file
from ovito.modifiers import SelectTypeModifier, DeleteSelectedModifier, ExpressionSelectionModifier
import sys

dump_file = sys.argv[1]

timestep = 2 # fs
#dumpfreq = 1000 # dump frequency

pipeline = import_file(dump_file)
print(str(pipeline.source.num_frames) + ' frames')

pipeline.modifiers.append(SelectTypeModifier(property = 'Particle Type', types = {'Type 3'}))
pipeline.modifiers.append(DeleteSelectedModifier())
pipeline.modifiers.append(ExpressionSelectionModifier(expression = '((Position.X - 120)^2+(Position.Y - 120)^2+(Position.Z - 120)^2)^(1/2) < 60'))

llist = []
output_file = 'evaporation_'+dump_file.strip().split('.')[-1]+'.dat'
with open(output_file, 'w') as f:
    for frame in range(pipeline.source.num_frames):
        data = pipeline.compute(frame)
        tmp = data.attributes['SelectExpression.num_selected'] / 3
        llist.append(str(frame*timestep) + " {:.3f}".format(tmp)+'\n')
    f.write(''.join(llist))
```
详细参考https://www.ovito.org/docs/current/python/index.html