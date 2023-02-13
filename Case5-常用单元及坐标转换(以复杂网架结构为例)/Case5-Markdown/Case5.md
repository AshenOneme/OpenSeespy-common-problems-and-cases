# Edited by Ashen; 微信公众号:爱研思谈; Github:AshenOneme


```python
import openseespy.opensees as ops
import opsvis as opsv
import matplotlib.pyplot as plt
import numpy as np
from matplotlib.ticker import MultipleLocator,FormatStrFormatter
from pylab import *
import os
```

## 二维坐标系下
![ElementCrossSection](https://user-images.githubusercontent.com/98397090/218360842-c342a223-9a53-4752-99b4-9b736cad3532.png)

#### 截面局部坐标系如下：
Y   
^   
|                 
|   
----------> Z
#### 结构整体坐标系如下：    
</p>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    Y      </p>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    ^      </p>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    |      </p>        
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;     
    |      </p>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    ----------> X   </p>
    &nbsp;&nbsp;&nbsp;&nbsp;
   /   </p>
   &nbsp;&nbsp;&nbsp;
  /   </p>
  &nbsp;&nbsp;
 /   </p>
 
Z </p>
</div>   

#### 二维坐标系下的截面坐标：
![ElementVectors](https://user-images.githubusercontent.com/98397090/218360884-dace6cb8-353a-4abc-8f13-b71d927e709f.png)




### 材料信息


```python
ops.wipe()
ops.model('basic', '-ndm', 2, '-ndf', 3)
IDSteel = 1
Fy_Steel = 400
E0_Steel = 206000
bs_Steel = 0.005
R0 = 12.5
cR1 = 0.925
cR2 = 0.15
ops.uniaxialMaterial('Steel02', IDSteel, Fy_Steel, E0_Steel, bs_Steel,R0,cR1,cR2)

IDCoverC=2
fpc_cover=-40
epsc0_cover=-0.002
fpcu_cover=-8
epsU_cover=-0.004
ops.uniaxialMaterial('Concrete01', IDCoverC, fpc_cover, epsc0_cover, fpcu_cover, epsU_cover)

IDCoreC=3
fpc_core=-40
epsc0_core=-0.0024
fpcu_core=-18
epsU_core=-0.006
ops.uniaxialMaterial('Concrete01', IDCoreC, fpc_core, epsc0_core, fpcu_core, epsU_core)
```

### 截面信息(Element1)


```python
Bbeam = 250
Hbeam = 500
c=30
y1beam = Hbeam/2.0
z1beam = Bbeam/2.0
y2beam = (Hbeam-2*c)/5.0
nFibZ=1
nFibZ_Core = 5
nFib=20
nFibCover, nFibY_Core = 1, 16
As_bar = 3.14*6*6
fiber_beam_section1=1
ops.section('Fiber', fiber_beam_section1)
ops.patch('rect', IDCoreC, nFibY_Core, nFibZ_Core, c-y1beam, c-z1beam, y1beam-c, z1beam-c)
ops.patch('rect', IDCoverC, nFib, nFibZ, -y1beam, -z1beam, y1beam, c-z1beam)
ops.patch('rect', IDCoverC, nFib, nFibZ, -y1beam, z1beam-c, y1beam, z1beam)
ops.patch('rect', IDCoverC, nFibCover, nFibZ_Core, -y1beam, c-z1beam, c-y1beam, z1beam-c)
ops.patch('rect', IDCoverC, nFibCover, nFibZ_Core, y1beam-c, c-z1beam, y1beam, z1beam-c)
ops.layer('straight', IDSteel, 2, As_bar, y1beam - c, z1beam - c, y1beam - c, c - z1beam)
ops.layer('straight', IDSteel, 2, As_bar, y2beam, z1beam - c, y2beam, c - z1beam)
ops.layer('straight', IDSteel, 2, As_bar, -y2beam, z1beam - c, -y2beam, c - z1beam)
ops.layer('straight', IDSteel, 2, As_bar, c - y1beam, z1beam - c, c - y1beam, c - z1beam)
fiber_beam_section_D=[['section', 'Fiber', 2],
                     ['patch', 'rect', IDCoreC, nFibY_Core, nFibZ_Core, c-y1beam, c-z1beam, y1beam-c, z1beam-c],
                     ['patch', 'rect', IDCoverC, nFib, nFibZ, -y1beam, -z1beam, y1beam, c-z1beam],
                     ['patch', 'rect', IDCoverC, nFib, nFibZ, -y1beam, z1beam-c, y1beam, z1beam],
                     ['patch', 'rect', IDCoverC, nFibCover, nFibZ_Core, -y1beam, c-z1beam, c-y1beam, z1beam-c],
                     ['patch', 'rect', IDCoverC, nFibCover, nFibZ_Core, y1beam-c, c-z1beam, y1beam, z1beam-c],
                     ['layer', 'straight', IDSteel, 4, As_bar, y1beam - c, z1beam - c, y1beam - c, c - z1beam],
                     ['layer', 'straight', IDSteel, 2, As_bar, y2beam, z1beam - c, y2beam, c - z1beam],
                     ['layer', 'straight', IDSteel, 2, As_bar, -y2beam, z1beam - c, -y2beam, c - z1beam],
                     ['layer', 'straight', IDSteel, 4, As_bar, c - y1beam, z1beam - c, c - y1beam, c - z1beam]]

matcolor = ['red', 'lightgrey', 'cyan', 'w', 'w', 'w']
opsv.plot_fiber_section(fiber_beam_section_D, matcolor=matcolor)
plt.axis('equal')
plt.show()
```


    

![output_6_0](https://user-images.githubusercontent.com/98397090/218360907-12432dd0-f5fc-4036-8881-7bf9b45cf4c6.png)
 


### 截面信息(Element2)


```python
Bbeam = 250
Hbeam = 500
c=30
y1beam = Hbeam/2.0
z1beam = Bbeam/2.0
z2beam = (Bbeam-2*c)/5.0
nFibZ=1
nFibZ_Core = 15
nFib=20
nFibCover, nFibY_Core = 1, 16
As_bar = 3.14*6*6
fiber_beam_section2=2
ops.section('Fiber', fiber_beam_section2)
ops.patch('rect', IDCoreC, nFibY_Core, nFibZ_Core, c-z1beam, c-y1beam, z1beam-c, y1beam-c)
ops.patch('rect', IDCoverC, nFib, nFibZ, -z1beam, -y1beam, z1beam, c-y1beam)
ops.patch('rect', IDCoverC, nFib, nFibZ, -z1beam, y1beam-c, z1beam, y1beam)
ops.patch('rect', IDCoverC, nFibCover, nFibZ_Core, -z1beam, c-y1beam, c-z1beam, y1beam-c)
ops.patch('rect', IDCoverC, nFibCover, nFibZ_Core, z1beam-c, c-y1beam, z1beam, y1beam-c)
ops.layer('straight', IDSteel, 4, As_bar, z1beam - c, y1beam - c, z1beam - c, c - y1beam)
ops.layer('straight', IDSteel, 2, As_bar, z2beam, y1beam - c, z2beam, c - y1beam)
ops.layer('straight', IDSteel, 2, As_bar, -z2beam, y1beam - c, -z2beam, c - y1beam)
ops.layer('straight', IDSteel, 4, As_bar, c - z1beam, y1beam - c, c - z1beam, c - y1beam)

fiber_beam_section_D=[['section', 'Fiber', 2],
                     ['patch', 'rect', IDCoreC, nFibY_Core, nFibZ_Core, c-z1beam, c-y1beam, z1beam-c, y1beam-c],
                     ['patch', 'rect', IDCoverC, nFib, nFibZ, -z1beam, -y1beam, z1beam, c-y1beam],
                     ['patch', 'rect', IDCoverC, nFib, nFibZ, -z1beam, y1beam-c, z1beam, y1beam],
                     ['patch', 'rect', IDCoverC, nFibCover, nFibZ_Core, -z1beam, c-y1beam, c-z1beam, y1beam-c],
                     ['patch', 'rect', IDCoverC, nFibCover, nFibZ_Core, z1beam-c, c-y1beam, z1beam, y1beam-c],
                     ['layer', 'straight', IDSteel, 4, As_bar, z1beam - c, y1beam - c, z1beam - c, c - y1beam],
                     ['layer', 'straight', IDSteel, 2, As_bar, z2beam, y1beam - c, z2beam, c - y1beam],
                     ['layer', 'straight', IDSteel, 2, As_bar, -z2beam, y1beam - c, -z2beam, c - y1beam],
                     ['layer', 'straight', IDSteel, 4, As_bar, c - z1beam, y1beam - c, c - z1beam, c - y1beam]]

matcolor = ['red', 'lightgrey', 'cyan', 'w', 'w', 'w']
opsv.plot_fiber_section(fiber_beam_section_D, matcolor=matcolor)
plt.axis('equal')
plt.show()
```



![output_8_0](https://user-images.githubusercontent.com/98397090/218360942-e91a1c77-6e73-42c3-bbea-6eb5041380bc.png)


    


#### 坐标转换


```python
coordTransf = "PDelta"  # Linear, PDelta, Corotational
IDColumnTransf2D = 1
ops.geomTransf(coordTransf, IDColumnTransf2D)
IDBeamTransf2D = 2
ops.geomTransf(coordTransf, IDBeamTransf2D)
IDFCSIntegration2D = 1
ops.beamIntegration('Trapezoidal', IDFCSIntegration2D, fiber_beam_section1,4)
IDFBSIntegration2D = 2
ops.beamIntegration('Trapezoidal', IDFBSIntegration2D, fiber_beam_section2,4)
```

## 三维坐标系下
#### 截面局部坐标的Z轴对准整体坐标哪个轴，输入相应坐标
![ElementOrientation](https://user-images.githubusercontent.com/98397090/218360999-e8c69a15-96cb-4ab2-b05d-a44aa50a5cdb.png)



```python
coordTransf = "PDelta"  # Linear, PDelta, Corotational
IDColumnTransf3D = 3
ops.geomTransf(coordTransf, IDColumnTransf3D,0,0,-1)
IDBeamTransf3D = 4
ops.geomTransf(coordTransf, IDBeamTransf3D,0,1,0)
IDFCSIntegration3D = 3
ops.beamIntegration('Trapezoidal', IDFCSIntegration3D, fiber_beam_section1,4)
IDFBSIntegration3D = 4
ops.beamIntegration('Trapezoidal', IDFBSIntegration3D, fiber_beam_section2,4)
```

# Edited by Ashen; 微信公众号:爱研思谈; Github:AshenOneme

### 网架结构案例(在OpenSeespy内进行坐标转换)


```python
import openseespy.opensees as ops
import opsvis as opsv
import matplotlib.pyplot as plt
import numpy as np
from matplotlib.ticker import MultipleLocator,FormatStrFormatter
from pylab import *
import os
```


```python
Nodes=np.loadtxt('Node.txt',skiprows=1,delimiter=',')
Elements=np.loadtxt('element.txt',skiprows=1,delimiter=',')
```


```python
ops.wipe()
ops.model('basic', '-ndm', 3, '-ndf', 6)
IDSteel = 1
Fy_Steel = 400
E0_Steel = 206000
bs_Steel = 0.005
R0 = 12.5
cR1 = 0.925
cR2 = 0.15
ops.uniaxialMaterial('Steel02', IDSteel, Fy_Steel, E0_Steel, bs_Steel,R0,cR1,cR2)
```


```python
HI=150
WI=150
cx=10
cy=7

A=[-HI/2-cx,-WI/2]
B=[-HI/2,WI/2]
C=[-HI/2,-cy/2]
D=[HI/2,cy/2]
E=[HI/2,-WI/2]
F=[HI/2+cx,WI/2]

I_section=1
ops.section('Fiber', I_section, '-GJ', 1.0e6)
ops.patch('rect', IDSteel, 1, 4, A[0], A[1], B[0], B[1])
ops.patch('rect', IDSteel, 4, 1, C[0], C[1], D[0], D[1])
ops.patch('rect', IDSteel, 1, 4, E[0], E[1], F[0], F[1])

fib_sec_1 = [['section', 'Fiber', 1, '-GJ', 1.0e6],
             ['patch', 'rect', IDSteel, 2, 8, A[0], A[1], B[0], B[1]],
             ['patch', 'rect', IDSteel, 10, 2, C[0], C[1], D[0], D[1]],
             ['patch', 'rect', IDSteel, 2, 8, E[0], E[1], F[0], F[1]],
             ]

matcolor = ['r']
opsv.plot_fiber_section(fib_sec_1, matcolor=matcolor)
plt.axis('equal')
plt.show()
```


    

![output_5_0](https://user-images.githubusercontent.com/98397090/218361040-4ebfcd25-f3c9-478a-8b7a-1a70d718413f.png)




```python
# %matplotlib widget
coordTransf = "PDelta"  # Linear, PDelta, Corotational

for node in Nodes:
    NodeTag=int(node[0])
    NodeTagX=node[1]
    NodeTagY=node[2]
    NodeTagZ=node[3]
    ops.node(NodeTag,NodeTagX,NodeTagY,NodeTagZ)
for element in Elements:
    ElementTag=int(element[0])
    ElementStart=int(element[1])
    ElementEnd=int(element[2])
    element_orientation=Nodes[ElementStart-1,1:4]-Nodes[ElementEnd-1,1:4]
    section_orientation=[-element_orientation[1]/element_orientation[0],1,0]
    L2=np.sqrt(section_orientation[0]**2+section_orientation[1]**2+section_orientation[2]**2)
    IDTransf=ElementTag
    IDIntegration=ElementTag
    
    X=section_orientation[0]/L2
    Y=section_orientation[1]/L2
    Z=section_orientation[2]/L2
    Transf=np.array([X,Y,Z])
    Transf[np.isnan(Transf)] = 1

    ops.geomTransf(coordTransf,IDTransf,Transf[0],Transf[1],Transf[2])

    ops.beamIntegration('Trapezoidal', IDIntegration, I_section,4)

    ops.element('dispBeamColumn',ElementTag,ElementStart,ElementEnd,IDTransf,IDIntegration)
    
fig=plt.figure(figsize=(20,20),dpi=200)
ax=fig.gca(projection='3d')
opsv.plot_model(ax=ax,node_labels=1,element_labels=0)
ax.view_init(90, 90, 'z')
ax.view_init(60, 60, 'z')
plt.gca().set_box_aspect((5., 5., 1.))
plt.show()

fig=plt.figure(figsize=(20,20),dpi=200)
ax=fig.gca(projection='3d')
opsv.plot_model(ax=ax,node_labels=1,element_labels=0)
ax.view_init(90, 90, 'z')
plt.gca().set_box_aspect((5., 5., 1.))
plt.show()

fig=plt.figure(figsize=(20,20),dpi=200)
ax=fig.gca(projection='3d')
opsv.plot_model(ax=ax,node_labels=1,element_labels=0)
ax.view_init(0, 0, 'z')
plt.gca().set_box_aspect((5., 5., 1.))
plt.show()
```

    C:\Users\Ashen\AppData\Local\Temp\ipykernel_55884\2052915022.py:15: RuntimeWarning: divide by zero encountered in double_scalars
      section_orientation=[-element_orientation[1]/element_orientation[0],1,0]
    C:\Users\Ashen\AppData\Local\Temp\ipykernel_55884\2052915022.py:20: RuntimeWarning: invalid value encountered in double_scalars
      X=section_orientation[0]/L2
    C:\Users\Ashen\AppData\Local\Temp\ipykernel_55884\2052915022.py:33: MatplotlibDeprecationWarning: Calling gca() with keyword arguments was deprecated in Matplotlib 3.4. Starting two minor releases later, gca() will take no keyword arguments. The gca() function should only be used to get the current axes, or if no axes exist, create new axes with default keyword arguments. To create a new axes with non-default arguments, use plt.axes() or plt.subplot().
      ax=fig.gca(projection='3d')
    


    
![output_6_1](https://user-images.githubusercontent.com/98397090/218361075-b9f978da-7d32-4973-b582-d5c64140314d.png)
![output_6_3](https://user-images.githubusercontent.com/98397090/218361093-38e6ffa6-eff1-473f-8a24-a4ed61fa57f6.png)
![output_6_5](https://user-images.githubusercontent.com/98397090/218361124-e67894c2-f5c4-45a1-99a2-7a27870ec82c.png)


    



```python
for i in range(58,74):
    ops.fix(i,1,1,1,0,0,0)
```


```python
#记录所有节点信息
from Picture import record_nodes
record_nodes()
```


```python
ops.recorder('Node', '-file', "Disp.txt","-time",'-node', 2,'-dof',3, 'disp')
ops.recorder('Node', '-file', "Reaction.txt","-time",'-nodeRange',58,73,'-dof',3, 'reaction')

ops.timeSeries('Linear', 11)
ops.pattern('Plain', 100,11)
for i in range(1,10):
    ops.load(i,0,0,-1,0,0,0)
for i in range(74,82):
    ops.load(i,0,0,-1,0,0,0)
ops.constraints("Penalty",1e15,1e15)
ops.numberer("RCM")
ops.system("BandGeneral")
ops.test('NormDispIncr', 1e-5, 2000)
ops.algorithm("KrylovNewton")
for i in range(1,10):
    ops.integrator("DisplacementControl",i,3,-1)
    ops.analysis("Static")
    ops.analyze(1000)
for i in range(74,82):
    ops.integrator("DisplacementControl",i,3,-1)
    ops.analysis("Static")
    ops.analyze(1000)

```
    


```python
#绘制动画
from Picture import draw
draw(580,20,1,0.1,10000,0,0)
ops.wipe()
```

    
    ==================================================================
    =       The paint module module is being loaded......            =
    =       Start time             		 2023-02-13 10:16:11         =
    =       This is dedicated to my research group                   =
    =       Adapted from Ashen       mailbox:2307603152@qq.com       =
    ==================================================================
    [31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m[31m█[0m 100.0%|100% 	已用时间: 12.36S


    <Figure size 1000x1000 with 0 Axes>



```python
disp=np.loadtxt('Disp.txt',usecols=1)
reaction=np.loadtxt('Reaction.txt')
reactionsum=np.sum(reaction[:,1:],axis=1)/1000
plt.plot(-disp,reactionsum)
plt.show()
```


    

![output_11_0](https://user-images.githubusercontent.com/98397090/218361196-ba3a6ba8-eb3f-4383-936b-23a0c0824b0d.png)


