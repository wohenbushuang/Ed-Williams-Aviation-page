[原始页面](http://edwilliams.org/intersect.htm) | [Backup of original English version](../en-us/intersect.md)

以下用于寻找由从 `点1 (lat1, lon1)` 到 `点2` 和从 `点3` 到 `点4` 的圆弧定义的两个大圆的交点。

-----------------

此处使用航空公式集中约定的“`W`经度为正”。（对于更常规的约定，请更改以下所有经度的符号。）

对于每个点，我们可以将它与一个从地球中心指向它的单位向量相关联，其组成部分是：
```
e ={ex,ey,ez} = {cos(lat)*cos(lon), -cos(lat)*sin(lon), sin(lat)} (1)
```
我们也可以以此逆推
```
lat=atan2(ez, sqrt(ex^2 + ey^2)); lon=atan2(-ey, ex) (2)
```
对于任意由点1和点2定义的大圆，点
```
P(e1,e2) =(e1 X e2)/||e1 X e2||
```
垂直于大圆的平面。（它的符号相反坐标即为相对立的点坐标）。

这里`e1 X e2`是向量叉积，其组成部分分别为
```
{e1y *e2z -e2y *e1z, e1z *e2x -e2z *e1x, e1x *e2y -e1y *e2x} (3)
```
（稍后可看到一个数字化靠谱计算这个的方法！）

`||e||`是向量的长度，定义式为
```
||e|| =sqrt(ex^2 + ey^2 + ez^2) (4)
```
大圆的交点可以看成是
```
+- P( P(e1,e2), P(e3,e4) )
```
由于舍入错误的原因，无法在短距离内直接计算叉积。应用一些三角恒等式可以得出：
```
e1 X e2 = { sin(lat1-lat2) *sin((lon1+lon2)/2) *cos((lon1-lon2)/2) -

sin(lat1+lat2) *cos((lon1+lon2)/2) *sin((lon1-lon2)/2) ,

sin(lat1-lat2) *cos((lon1+lon2)/2) *cos((lon1-lon2)/2) +

sin(lat1+lat2) *sin((lon1+lon2)/2) *sin((lon1-lon2)/2) ,

cos(lat1)*cos(lat2)*sin(lon1-lon2) } (5)
```
便可规避这个问题。

# 算法：

使用公式 (5) 计算 `e1 X e2` 和 `e3 X e4` 。

使用公式 (4) 归一化 `ea= (e1 X e2)/ ||e1 X e2||` 和 `eb=(e3 x e4)/||e3 X e4||`

使用公式 (3) 计算 `ea X eb`

使用公式 (2) 逆推（这里不需要先归一化）

两个候选交点便为 `(lat,lon)` 以及对立的 `(-lat, lon+pi)`

 