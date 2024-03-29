# 航空公式集 V1.46
[原始页面](http://edwilliams.org/avform.htm) | [Backup of original English version](../en-us/avform.md)

### 原作者 Ed Williams

#### 目录
- [大圆法导航公式概述](#概述)
- [大圆法导航公式](#大圆法导航公式)
  - [点间距离](#点间距离)
  - [点间航线](#点间航线)
  - [大圆上点的纬度](#大圆上点的纬度)
  - [给定径向和距离求纬度/经度](#给定径向和距离求纬度经度)
  - [径向的交点](#径向的交点)
  - [克莱劳公式](#克莱劳公式)
  - [大圆与纬线交点](#纬线交点)
  - [大圆上的点](#大圆上的点)
  - [横向轨迹误差与沿航线距离](#横向轨迹误差)
  - [与大圆已知距离的点](#与大圆已知距离的点)
- [实现说明](#实现说明)
  - [Atan, atan2, acos, asin 和 mod 函数](#数学函数说明)
  - [符号约定](#符号约定)
- [有效示例](#有效示例)
- [一些通用的球面三角形公式](#一些通用的球面三角形公式)
- [`球面直角三角形`](#球面直角三角形)
- [等角航法导航](#等角航法导航)
- [局地地球平面近似](#局地地球平面近似)
- [风速三角形](#风速三角形)
- [Head and cross-winds](#head--and-cross-wind-components)
- [TAS and wind speed from three groundspeeds](#tas-and-windspeed-from-three-gps-groundspeeds)
- [Variation](#approximate-variation-formulae)
- [Altimetry and the standard atmosphere formulae](#standard-atmosphere-and-altimetry)
- [Mach numbers, true vs calibrated airspeeds etc.](#mach-numbers-true-vs-calibrated-airspeeds-etc)
- [Relative humidity related to temperature and dewpoint or frostpoint](#relative-humidity-dewpoint-frostpoint-etc)
- [Bellamy's formula for the wind drift](#bellamys-formula)
- [单位转换等](#单位转换等)
- [Turns and pivotal altitude.](#turns-and-pivotal-altitude)
- [Distance to the horizon.](#distance-to-horizon)
- [Revision History](#revision-history)

[使用椭圆地球模型的Javascript计算器](http://edwilliams.org/gccalc.htm)

[日出-日落算法](http://edwilliams.org/sunrise_sunset_algorithm.htm)

[日出-日落示例](http://edwilliams.org/sunrise_sunset_example.htm)

[罗经误差](http://edwilliams.org/compass/compass.html)

[在球形地球上导航](http://edwilliams.org/ellipsoid/ellipsoid.html)

---

#### 概述

本概述是给对大圆法导航感兴趣并想知道如何计算航向，首向和其他兴趣点的飞行员（和其他人）编写的。这些公式可以编程到你的计算器或电子表格中。我将尝试提供足够的信息，以便熟悉平面三角学的人可以根据需要得出其他结果。

众所周知，两点之间的最短距离是一条直线。但是，任何试图从连接从洛杉矶飞往纽约的直线的人都必须首先挖出一条非常坚固的隧道。*沿着地球表面*的最短距离垂直位于上述直线路径的上方。这条路线可以通过将地球用通过洛杉矶国际机场（IATA: LAX）和纽约肯尼迪机场（IATA: JFK）的虚构的平面切成两半来构造。该平面以连接两个点的圆弧切开地球（假定为球形），称为*大*圆。只有穿过地球中心的平面才会产生大圆。任何平面都会在一个球体上切出一个圆，但是由此产生的小圆圈并不是它们所连接的点之间的最短距离。稍加思考，就会发现经线（子午线）是大圆，但是纬线（除赤道外）不是。

我将假定读者熟悉纬度和经度，以此作为指定地球表面位置的一种手段。顾及北美人方便，我将北纬和西经定为正，南北经定为负。经度与通常的数学惯例相反。真航向与平常一样，定义为航向线与局部子午线之间沿顺时针方向测量的角度。

要意识到的第一个重要事实是，大圆路线的真实路线随点而异。例如，纬度相等（非零）的两个点之间的大圆路径不在E-W方向纬线上，而是个朝极点的弧线。使用不变的真航向在两点之间飞行*确实*可行，但通常所得到的路线与大圆路线不同，被称为等角路线。不同于以闭合曲线环绕地球的大圆，等角飞行的飞行员将向极点螺旋靠近。

自然地，问题就在于去寻找两个指定点之间的大圆距离以及沿路线的点处的真实路线。如果以适当的自然单位，即都以弧度测量角度和距离，则所需的球形三角公式将大大简化！根据定义，一弧度是单位长度和单位半径的圆弧所夹的角度。由于单位半径的完整圆弧的长度为`2*pi`，所以就有换算360度等于`2*pi`弧度，或者：
```
      angle_radians=(pi/180)*angle_degrees
      angle_degrees=(180/pi)*angle_radians
```

通过将距离定义为弧线对向地球中心的角度，大圆距离同样可以用弧度表示。由于按照定义，一海里相当于一分（= `1/60`度）的弧度，所以我们有：
```
      distance_radians=(pi/(180*60))*distance_nm
      distance_nm=((180*60)/pi)*distance_radians
```

在后续所有公式中，所有距离和角度（例如纬度，经度和真航向）均假定为弧度，从而大大简化；在实际应用中上述公式及其逆推对于在自然单位和实用单位之间来回转换是必需的。稍后给出此过程的示例。

注意：目前，海里定义为1852米——与历史定义一致，意味着地球半径为`1.852 *（180 * 60 / pi）= 6366.71 km`，这确实在当前接受的（WGS84）赤道半径`6378.137 km`和极地半径`6356.752 km`范围内。在此范围内，地球半径的其他选择仍符合球面近似，并且可能出于某些特殊目的作为首选。

例如，以前用于航空记录的FAI标准使用半径为6371.0 km的“FAI球体”。

要使用其他球形半径，请使用
```
      distance_km = radius_km * distance_radians 
      distance_radians = distance_km/radius_km
```
在角度和距离单位之间转换。

你可以[在此](http://edwilliams.org/ftp/avsig/avform.xls)找到Excel电子表格形式的许多公式的示例实现。为了便于阅读，这些公式位于VBA宏中，因此你需要启用宏才能使其工作。如果你无法通过禁用宏（以检查病毒）等方式打开电子表格，则可能需要打Excel补丁。Excel 97 SR-2可以尝试http://office.microsoft.com/downloaddetails/Xl8p9pkg.htm。

*如果你决定对这些公式进行编程，则建议你查看[实现说明](#implement)，并根据[有效示例](#example)和电子表格检查结果。*

---

#### 大圆法导航公式
##### 点间距离
坐标为`{lat1，lon1}`和`{lat2，lon2}`的两点之间的大圆距离`d`为：
```
d=acos(sin(lat1)*sin(lat2)+cos(lat1)*cos(lat2)*cos(lon1-lon2))
```
一个数学上等效的，在短距离内较少受到舍入误差的影响公式为：
```
d=2*asin(sqrt((sin((lat1-lat2)/2))^2 + 
                 cos(lat1)*cos(lat2)*(sin((lon1-lon2)/2))^2))
```
---
##### 点间航向
我们通过以下方法获得从点1到点2的初始航向`tc1`（在点1）。如果起始点是极点，则公式将失效。对于特殊情况我们可以使用：
```
IF (cos(lat1) < EPS)   // EPS 一个考虑机器精度的小数字
  IF (lat1 > 0)
     tc1= pi        //  从北极点出发
  ELSE
     tc1= 2*pi         //  从南极点出发
  ENDIF
ENDIF
```
对于两极以外的起点：
```
IF sin(lon2-lon1)<0       
   tc1=acos((sin(lat2)-sin(lat1)*cos(d))/(sin(d)*cos(lat1)))    
ELSE       
   tc1=2*pi-acos((sin(lat2)-sin(lat1)*cos(d))/(sin(d)*cos(lat1)))    
ENDIF
```
不需要预先计算`d`，点之间的距离，的另一种公式是：
```
   tc1=mod(atan2(sin(lon1-lon2)*cos(lat2),
           cos(lat1)*sin(lat2)-sin(lat1)*cos(lat2)*cos(lon1-lon2)), 2*pi)
```
---
##### 大圆上点的纬度
在以下情况下，中间点`{lat，lon}`位于连接了点1和2的大圆上：
```
lat=atan((sin(lat1)*cos(lat2)*sin(lon-lon2)
     -sin(lat2)*cos(lat1)*sin(lon-lon1))/(cos(lat1)*cos(lat2)*sin(lon1-lon2)))
```
（不适用于子午线。即当`sin(lon1-lon2)=0`）

---
##### 给定径向和距离求纬度/经度
点`{lat，lon}`在点1`tc`径向上且距离为`d`时：
```
     lat=asin(sin(lat1)*cos(d)+cos(lat1)*sin(d)*cos(tc))
     IF (cos(lat)=0)
        lon=lon1      // endpoint a pole
     ELSE
        lon=mod(lon1-asin(sin(tc)*sin(d)/cos(lat))+pi,2*pi)-pi
     ENDIF
```
这个算法限于距离满足`dlon < pi / 2`，即在经度上延伸不到地球周长的四分之一。对于更大的距离，则需要一个完全通用但更复杂的算法：
```
     lat =asin(sin(lat1)*cos(d)+cos(lat1)*sin(d)*cos(tc))
     dlon=atan2(sin(tc)*sin(d)*cos(lat1),cos(d)-sin(lat1)*sin(lat))
     lon=mod( lon1-dlon +pi,2*pi )-pi
```
---
##### 径向的交点
现在计算由点1的`crs13`真方位和点2的`crs23`真方位形成的交点的纬度`lat3`和经度`lon3`：
```
dst12=2*asin(sqrt((sin((lat1-lat2)/2))^2+
                   cos(lat1)*cos(lat2)*sin((lon1-lon2)/2)^2))
IF sin(lon2-lon1)<0
   crs12=acos((sin(lat2)-sin(lat1)*cos(dst12))/(sin(dst12)*cos(lat1)))
   crs21=2.*pi-acos((sin(lat1)-sin(lat2)*cos(dst12))/(sin(dst12)*cos(lat2)))
ELSE
   crs12=2.*pi-acos((sin(lat2)-sin(lat1)*cos(dst12))/(sin(dst12)*cos(lat1)))
   crs21=acos((sin(lat1)-sin(lat2)*cos(dst12))/(sin(dst12)*cos(lat2)))
ENDIF

ang1=mod(crs13-crs12+pi,2.*pi)-pi
ang2=mod(crs21-crs23+pi,2.*pi)-pi

IF (sin(ang1)=0 AND sin(ang2)=0)
   "无数个交点"
ELSEIF sin(ang1)*sin(ang2)<0
   "交点不明"
ELSE
   ang1=abs(ang1)
   ang2=abs(ang2)
   ang3=acos(-cos(ang1)*cos(ang2)+sin(ang1)*sin(ang2)*cos(dst12)) 
   dst13=atan2(sin(dst12)*sin(ang1)*sin(ang2),cos(ang2)+cos(ang1)*cos(ang3))
   lat3=asin(sin(lat1)*cos(dst13)+cos(lat1)*sin(dst13)*cos(crs13))
   dlon=atan2(sin(crs13)*sin(dst13)*cos(lat1),cos(dst13)-sin(lat1)*sin(lat3))
   lon3=mod(lon1-dlon+pi,2*pi)-pi
ENDIF
```
点1、2和（如果唯一）交点3组成一个球面三角形，其内角为`abs(ang1)`、`abs(ang2)`和`ang3`。要找到两个大圆对应的交点对，请参考以下。

---
[两个大圆的交点](intersect.md)

---
##### 克莱劳公式：
这与任何大圆的纬度（lat）和真航向（tc）有关，即：`sin(tc)*cos(lat)=常数`。也就是说，对于大圆上的任何两点：
```
           sin(tc1)*cos(lat1)=sin(tc2)*cos(lat2)
```
由于达到最高纬度（`latmx`）时，`tc`肯定为`90`或`270`，因此我们还有：
```
latmx=acos(abs(sin(tc)*cos(lat))) 
```
其中`lat`和`tc`是大圆上**任意**点的纬度和真航向。

---
##### 纬线交点：
任何大圆（通过两极的除外）都只能跨一次子午线。但是，任何大圆（赤道除外）在其顶点都具有最大的纬度。它两侧向低纬度跨越，并不向高纬度跨越。因此，用于查找大圆与给定平行线相交点的经度的算法稍微复杂一些。

假设一个大圆通过`(lat1,lon1)`和`(lat2,lon2)`。它和纬线`lat3`相交于经度`lon3_1`和`lon3_2`：
```
   l12 = lon1-lon2
   A = sin(lat1)*cos(lat2)*cos(lat3)*sin(l12)
   B = sin(lat1)*cos(lat2)*cos(lat3)*cos(l12) - cos(lat1)*sin(lat2)*cos(lat3)
   C = cos(lat1)*cos(lat2)*sin(lat3)*sin(l12)
   lon = atan2(B,A)                      ( atan2(y,x) convention)
  IF (abs(C) >sqrt(A^2 + B^2))
    "no crossing"
  ELSE
    dlon = acos(C/sqrt(A^2+B^2))
    lon3_1=mod(lon1+dlon+lon+pi, 2*pi)-pi
    lon3_2=mod(lon1-dlon+lon+pi, 2*pi)-pi
  ENDIF
```
---
##### 大圆上的点
在前面的部分中，我们在给定的相交纬度或经度上找到了大圆上的点。这里，我们寻找两点之间距离（`d`）的给定分数的点`(lat,lon)`。假设起点是`(lat1,lon1)`，终点是`(lat2,lon2)`，我们希望沿大圆路径该点为分数`f`。`f=0`即点1。`f=1`即点2。这两个点不能是相对的（即`lat1+lat2=0`和`abs(lon1-lon2)=pi`），因为这样路线是不确定的。接着，中间点的经度由下式给出：
```
        A=sin((1-f)*d)/sin(d)
        B=sin(f*d)/sin(d)
        x = A*cos(lat1)*cos(lon1) +  B*cos(lat2)*cos(lon2)
        y = A*cos(lat1)*sin(lon1) +  B*cos(lat2)*sin(lon2)
        z = A*sin(lat1)           +  B*sin(lat2)
        lat=atan2(z,sqrt(x^2+y^2))
        lon=atan2(y,x)
```
---
##### 横向轨迹误差：
假设你正沿着从A到B的大圆路线（`航向=crs_AB`），结果到了D，也许偏离了路线。（我们假设A不是极点！）你可以使用上述公式计算从A到D的航向（`crs_AD`）和从A到D的距离（`dist_AD`）。就这些量而言，横向轨迹误差`XTD`（偏离航线距离）由下式给出：
```
           XTD =asin(sin(dist_AD)*sin(crs_AD-crs_AB))
（正值XTD代表在航向右侧，负值代表在左侧）
（如果点A是北极点或南极点，相应地替换crs_AD-crs_AB为lon_D-lon_B或lon_B-lon_D。）
```
“沿航线距离”（`ATD`），即从A沿着向B航线到正横着D的点的距离，由下式给出：
```
                 ATD=acos(cos(dist_AD)/cos(XTD))
```
对于非常短的距离：
```
                 ATD=asin(sqrt( (sin(dist_AD))^2 - (sin(XTD))^2 )/cos(XTD))
```
舍入误差更小

请注意，我们还可以使用上面的公式找到通过A和B的大圆上最接近点D的点

##### 与大圆已知距离的点
假设点A和点B定义一条大圆航线，而点D是第三点。在通过A和B的大圆上找到与D距离为`d`的点，如果存在的话。
```
   A = crs_AD - crs_AB
```
（`crs_AB`和`crs_AD`分别即从A到B和D的初始大圆航向。使用[点间航向](#点间航向)进行计算）
```
   b = dist_AD
```
（`dist_AD`即从A到D的距离。使用[点间距离](#点间距离)进行计算）
```
   r=(cos(b)^2+sin(b)^2*cos(A)^2)^(1/2)
```
（`acos(r)`即`XTD`）
```
   p=atan2(sin(b)*cos(A),cos(b))
```
（`p`即`ATD`）
```
   IF (cos(d)^2 > r^2) THEN
      No points exist
   ELSE
      Two points exist
     dp = p +- acos(cos(d)/r)
   ENDIF
```
`dp`即从A沿大圆AB到目标点的距离。他们的纬度/经度可以使用[给定径向和距离求纬度/经度](#给定径向和距离求纬度经度)进行计算

---
#### 实现说明：
##### 数学函数说明
注意：`^`表示幂运算符，`sqrt`是平方根函数，`acos`是反余弦（或逆余弦）函数，`asin`是反正弦函数。如果`asin`或`acos`不可用，则可以使用`atan2`函数实现它们：
```
acos(x)=atan2(sqrt(1-x^2),x)
  acos 返回值范围 0 <= acos <= pi
asin(x)=atan2(x,sqrt(1-x^2))}
  asin 返回值范围 -pi/2 <= asin <= pi/2
```
注意：此处`atan2`具有常规（C）的顺序参数，即`atan2(y,x)`。这不是通用的，例如Excel使用`atan2(x,y)`，但是无论如何它都有`asin`和`acos`。提醒，它返回`-pi < atan2 <= pi`范围内的值。

进一步注意：如果你的计算器/编程语言非常贫乏以至于只有`atan`可用，请使用：
```
   asin(x)=2*atan(x/(1+sqrt(1-x*x)))   
   acos(x)=2*atan(sqrt((1-x)/(1+x)))       x>=0
          =pi - 2*atan(sqrt((1+x)/(1-x)))  x<0

   atan2(y,x)=atan(y/x)       x>0
   atan2(y,x)=atan(y/x)+pi    x<0, y>=0
   atan2(y,x)=pi/2            x=0, y>0
   atan2(y,x)=atan(y/x)-pi    x<0, y<0
   atan2(y,x)=-pi/2           x=0, y<0
   atan2(0,0)未定义，应该给出一个错误。
```
另一个潜在的实现问题是，由于舍入误差，`asin`和/或`acos`的参数绝对值可能会超过一。这在完美的算法中是不会发生的。你可能需要使用以下行中`asin`和`acos`的“安全”版本：
```
  asin_safe(x)=asin(max(-1,min(x,1)))
  acos_safe(x)=acos(max(-1,min(x,1)))
```
注意`mod`函数。似乎对于不同编程语言的实现，结果的符号是跟随除数还是被除数的符号有着不同的约定。（我们希望符号[跟随除数或为欧几里得的](http://en.wikipedia.org/wiki/Modulo_operation)。C的`fmod`和Java的`％`无法适用。）在本文档中，`Mod(y,x)`是`y`除以`x`的余数，并且始终在`0 <= mod < x`范围内。例如：`mod(2.3,2.)=0.3`及`mod(-2.3,2.)=1.7`

如果你有`floor`函数（在Excel中为`int`），返回`floor(x)=“小于或等于x的最大整数”`，例如`floor(-2.3)=-3`及`floor(2.3)=2`
```
        mod(y,x) = y - x*floor(y/x)
```
在没有`floor`函数的情况下，无论“`int`”是截断还是向下舍入，以下应该适用：
 ```
    mod=y - x * int(y/x)
    if ( mod < 0) mod = mod + x
```
##### 符号约定
如概述所言，北纬和*西*经被视为正，南纬和东经为负。直接遵循会比较容易，但是如果你喜欢其他约定，你可以更改公式中的符号。

---
#### 有效示例：
``` 
  假设点1是洛杉矶国际机场（IATA: LAX）：(北纬 33度 57分, 西经 118度 24分)
  假设点2是纽约肯尼迪机场（IATA: JFK）: (北纬 40度 38分, 西经 73度 47分)
```
在弧度制中LAX：
```
lat1=(33+57/60)*pi/180=0.592539, lon1=(118+24/60)*pi/180=2.066470
```
以及JFK: 
```
(lat2=0.709186,lon2=1.287762)
```
从LAX到JFK的距离是
```
    d = 2*asin(sqrt((sin((lat1-lat2)/2))^2+
                   cos(lat1)*cos(lat2)*(sin((lon1-lon2)/2))^2))
      = 2*asin(sqrt((sin((0.592539-0.709186)/2))^2+
                   cos(0.592539)*cos(0.709186)*(sin((2.066470-1.287762)/2))^2))
      = 2*asin(sqrt((-0.05829)^2 +0.829525*0.758893*0.379591^2))
      = 2*asin(0.306765)
      = 0.623585 radians
      = 0.623585*180*60/pi=2144nm
 或

    d = acos(sin(lat1)*sin(lat2)+cos(lat1)*cos(lat2)*cos(lon1-lon2))
      = acos(sin(0.592539)*sin(0.709186)+
                     cos(0.592539)*cos(0.709186)*cos(0.778708))
      = acos(0.811790)
      = 0.623585 radians
      = 0.623585*180*60/pi=2144nm
```
从LAX出发的初始真航向是：
```
   sin(-0.778708)=-0.702<0 所以

       tc1 = acos((sin(lat2)-sin(lat1)*cos(d))/(sin(d)*cos(lat1)))
           = acos((sin(0.709186)-sin(0.592539)*cos(0.623585))/
                (sin(0.623585)*cos(0.592535))
           = acos(0.408455)
           = 1.150035 radians
           = 66 degrees  
```
LAX的66度径向外100nm的航路点（沿到JFK大圆100nm）的纬度和经度分别为：
```  
   100nm = 100*pi/(180*60)=0.0290888radians
     lat = asin(sin(lat1)*cos(d)+cos(lat1)*sin(d)*cos(tc))
         = asin(sin(0.592539)*cos(0.0290888)
              +cos(0.592539)*sin(0.0290888)*cos(1.150035))
         = asin(0.568087)
         = 0.604180radians
         = 北纬 34度 37分 

     lon = mod(lon1-asin(sin(tc)*sin(d)/cos(lat))+pi,2*pi)-pi
         = mod(2.066470- asin(sin(1.150035)*sin(0.0290888)/cos(0.604180))+pi,2*pi)-pi
         = mod(2.034206+pi,2*pi)-pi radians
         = 2.034206 radians
         = 西经 116度 33分
```
从LAX到JFK的大圆航线与西经111度子午线相交的纬度为:
```
    (111degrees=1.937315 radians)

  lat = atan((sin(lat1)*cos(lat2)*sin(lon-lon2)
        -sin(lat2)*cos(lat1)*sin(lon-lon1))/(cos(lat1)*cos(lat2)*sin(lon1-lon2)))
      = atan((sin(0.592539)*cos(0.709186)*sin(0.649553)
         -sin(0.709186)*cos(0.592539)*sin(-0.129154))/(cos(0.592539)*cos(0.709186)
                                                      *sin(0.778708)))
      = atan(0.737110)
      = 0.635200radians
      = 36 degrees 24min
```
横向轨迹误差

假设从LAX到JFK的路线上你发现自己位于`(D)`北纬34:30 西经116:30，其弧度为`(0.6021386,2.033309)`（参见前面LAX和JFK的坐标和航向）

从LAX到点D的距离为：
```
   dist_AD = acos(sin(0.592539)*sin(0.6021386)+
               cos(0.592539)*cos(0.6021386)*cos(2.066470-2.033309))
           = 0.02905 radians (99.8665 nm)
```
从LAX到点D的航向为：
```
    crs_AD = acos((sin(0.6021386)-sin(0.592539)*cos(0.02905))/
                               (sin(0.02905)*cos(0.592539)))
           = 1.22473 radians  (70.17 degrees)
```
点D的横向轨迹误差为：
```
      xtd = asin(sin(0.02905)*sin(1.22473-1.15003))
          = 0.00216747 radians
          = 0.00216747*180*60/pi =7.4512 nm 航线右侧

      atd = acos(cos(0.02905)/cos(0.00216747))
          = 0.0289691 radians
          = 0.0289691*180*60/pi = 99.588 nm 沿航线
```
从LAX到JFK航线的`40%`的点在：
```
       d = 2*asin(sqrt((sin((lat1-lat2)/2))^2+
                   cos(lat1)*cos(lat2)*(sin((lon1-lon2)/2))^2))
         = 2*asin(sqrt((-0.05829)^2 +0.829525*0.758893*0.379591^2))
         = 2*asin(0.306765)
         = 0.623585 radians （如前）
       f = 0.4    (40%)
       A = sin((1-f)*d)/sin(d)
         = sin(0.6*0.623585)/sin(0.623585)
         = 0.62588
       B = sin(f*d)/sin(d)
         = sin(0.4*0.623585)/sin(0.623585)
         = 0.422735
       x = A*cos(lat1)*cos(lon1) +  B*cos(lat2)*cos(lon2)
         = -0.157344
       y = A*cos(lat1)*sin(lon1) +  B*cos(lat2)*sin(lon2)
         = 0.764745
       z = A*sin(lat1)           +  B*sin(lat2)
         = 0.624826
     lat = atan2(z,sqrt(x^2+y^2))
         = 0.674909 radians
         = 北纬 38度 40.167分
     lon = atan2(y,x)
         = 1.77371 radians
         = 西经 101度 37.570分
```
交点计算示例（简要地）：
```
设点1为纽约州奥奈达县罗马机场（IATA: REO） (42.600N,117.866W)=(0.74351,2.05715)rad
设点2为俄勒冈州贝克郡贝克市贝克机场（IATA: BKE） (44.840N,117.806W)=(0.782606,2.056103)rad 
```
从REO出发的51度`(=0.890118rad)`航向线与从BKE出发的137度`(=2.391101rad)`航向线相交于`(lat3,lon3)`：

那么：
```
dst12=0.039103
crs12=0.018996
crs21=3.161312
ang1=0.871122
ang2=0.770211
ang3=1.500667
dst13=0.027290
(dst23=0.029986)
lat3=0.760473      =43.572N
lon3=2.027876      =116.189W  在爱达荷州埃达郡博伊西市博伊西机场（IATA: BOI）!
```
---
#### 一些通用的球面三角形公式
球面三角形是指其边都是大圆圆弧的三角形。设边的长度为`a`、`b`和`c`弧度，相对的角度为`A`、`B`和`C`弧度。
```
                c
           A -------B
            \       |
             \      |
              \b    |a
               \    |
                \   |
                 \  |
                  \C|
                   \|

    （角B不需要是直角）

      sin(a)  sin(b)   sin(c)
      ----- = ------ = ------
      sin(A)  sin(B)   sin(C)
    
  cos(a)=cos(b)*cos(c)+sin(b)*sin(c)*cos(A)
  cos(b)=cos(c)*cos(a)+sin(c)*sin(a)*cos(B)
  cos(c)=cos(a)*cos(b)+sin(a)*sin(b)*cos(C)

  cos(A)=-cos(B)*cos(C)+sin(B)*sin(C)*cos(a)
  cos(B)=-cos(C)*cos(A)+sin(C)*sin(A)*cos(b)
  cos(C)=-cos(A)*cos(B)+sin(A)*sin(B)*cos(c)

  这些式子的一些有用的结果：

  tan(A)=sin(B)*sin(a)/(sin(c)*cos(a)-cos(B)*cos(c)*sin(a))
  tan(B)=sin(C)*sin(b)/(sin(a)*cos(b)-cos(C)*cos(a)*sin(b))
  tan(C)=sin(A)*sin(c)/(sin(b)*cos(c)-cos(A)*cos(b)*sin(c))

  tan(a)=sin(b)*sin(A)/(sin(C)*cos(A)+cos(b)*cos(C)*sin(A))
  tan(b)=sin(c)*sin(B)/(sin(A)*cos(B)+cos(c)*cos(A)*sin(B))
  tan(c)=sin(a)*sin(C)/(sin(B)*cos(C)+cos(a)*cos(B)*sin(C)) 
```
给定`{a,b,c,A,B,C}`中**任意**三个，剩余的边和角可以用这些式子求出。球面三角形的求解（要求`0 < a,b,c,A,B,C < pi`以避免无效组合）：
```
  给定{A,b,c}：  // 2条边，夹1个角
   a=acos(cos(b)*cos(c)+sin(b)*sin(c)*cos(A))
   B=acos((cos(b) - cos(c)*cos(a))/(sin(c)*sin(a)))
   C=acos((cos(c) - cos(a)*cos(b))/(sin(a)*sin(b)))

  给定{a,B,C}：  // 2个角，夹1条边
   A=acos(-cos(B)*cos(C)+sin(B)*sin(C)*cos(a))
   b=atan2(sin(a)*sin(B)*sin(C),cos(B)+cos(C)*cos(A))
   c=atan2(sin(a)*sin(B)*sin(C),cos(C)+cos(A)*cos(B))

  给定{a,b,c}：  // 3条边
   A=acos((cos(a) - cos(b)*cos(c))/(sin(b)*sin(c)))
   B=acos((cos(b) - cos(c)*cos(a))/(sin(c)*sin(a)))
   C=acos((cos(c) - cos(a)*cos(b))/(sin(a)*sin(b)))

  给定{A,B,C}：  // 三个角（对于平面三角形而言这有无数个解，所以对于小的球面三角形在数值上可能不准确）
   delta=(A+B+C-pi)/2
   a=2*asin(sqrt(sin(delta)*sin(A-delta)/(sin(B)*sin(C))))
   b=2*asin(sqrt(sin(delta)*sin(B-delta)/(sin(C)*sin(A))))
   c=2*asin(sqrt(sin(delta)*sin(C-delta)/(sin(A)*sin(B))))

  给定{A,a,b}：  // 2条边，不夹的1个角
   x=sin(A)*sin(b)/sin(a)
   if (x=1) {
     B=pi/2    // 存在1个球面三角形
   } else if (x < 1) {
     B= asin(x) and pi-asin(x) // 存在2个三角形
   } else{
     // 三角形不存在
   }
   对于每一个三角形
   c=mod(2*atan2(cos((A+B)/2)*sin((a+b)/2),cos((A-B)/2)*cos((a+b)/2)),2*pi)
   C=mod(2*atan2(cos((a-b)/2)*cos((A+B)/2),cos((a+b)/2)*sin((A+B)/2)),2*pi)

  给定{a,A,B}：  // 2个角，不夹的1条边
   x=sin(a)*sin(B)/sin(A)
   if (x=1) {
     b=pi/2    // 存在1个三角形
   } else if (x < 1) {
     b=asin(x) and pi-asin(x) // 存在2个三角形 
   } else{
     // 三角形不存在
   }
   对于每一个三角形
   c=mod(2*atan2(cos((A+B)/2)*sin((a+b)/2),cos((A-B)/2)*cos((a+b)/2)),2*pi)
   C=mod(2*atan2(cos((a-b)/2)*cos((A+B)/2),cos((a+b)/2)*sin((A+B)/2)),2*pi)
```
对于球形三角形，内角`A+B+C`并不是`pi`（180度），而是更大一些。这个差值称为球面超`E`，定义为`E=A+B+C-pi`。

球面三角形包围的表面积由下式表示
```
Area = E*R^2
```
用边表示：
```
    E = 4*atan(sqrt(tan(s/2)*tan((s-a)/2)*tan((s-b)/2)*tan((s-c)/2)))
```
其中
```
    s = (a+b+c)/2
```
这是l'Huiller公式，类似于平面三角形的Heron公式。请注意，这在小三角形的限制内，在数值上表现良好。

其他一些可能偶尔有用的公式是：
```
   sin(A/2) = sqrt((sin(s-b)*sin(s-c))/(sin(b)*sin(c)))
   cos(A/2) = sqrt((sin(s)*sin(s-a))/(sin(b)*sin(c)))
   tan(A/2) = sin((b-c)/2)/(sin((b+c)/2)*tan((B-C)/2))
            = cos((b-c)/2)/(cos((b+c)/2)*tan((B+C)/2))
   tan(a/2) = cos((B+C)/2)*tan((b+c)/2)/cos((B-C)/2)
            = sin((B+C)/2)*tan((b-c)/2)/sin((B-C)/2)
   tan((A-B)/2)=cot(C/2)*sin((a-b)/2)/sin((a+b)/2)
   tan((A+B)/2)=cot(C/2)*cos((a-b)/2)/cos((a+b)/2)
   sin(a)*cos(B)=cos(b)*sin(c)-sin(b)*cos(c)*cos(A)
   cos(a)*cos(C)=sin(a)*cot(b)-sin(C)*cot(B)
```

在这些公式的任何一个中，`A`、`B`和`C`可以互换，只要`a`、`b`和`c`跟着一起改变。也就是`a->b, b->c, c->a, A->B, B->C, C->A`。另外，如果 `pi-a`表示`A`、`pi-b`表示`B`及`pi-c`表示`C`或其他类似情况，公式依然成立。也就是`A->pi-a, B->pi-b, C->pi-c, a->pi-A, b->pi-B, c->pi-C`
#### 球面直角三角形
If the angle B is a right angle, there are ten relations ( Napier's rules) that allow computing any unknown side or angle in terms of any two of the others:如果角`B`是直角，则有可以根据任何其他两个量来计算任何未知的边或角的十种关系（Napier法则）：
```
  cos(A) = sin(C)*cos(a)
  cos(C) = sin(A)*cos(c)
  sin(a) = sin(A)*sin(b)
  sin(c) = sin(C)*sin(b)
  tan(a) = tan(b)*cos(C) = sin(c)*tan(A)
  tan(c) = tan(b)*cos(A) = sin(a)*tan(C)
  cos(b) = cos(a)*cos(c) = 1/(tan(A)*tan(C))
```
#### 等角航法导航
等角线或者恒向线是指真航向为定值的航线。除了子午线和赤道外，它们与大圆并不相同。接近任一极点时它们都用处不大，因为它们会形成紧密缠绕的螺旋形。如果某一点确实*是*极点，则下面的公式将失效。

东-西方向的等角线很特别。它们贴合纬线并形成闭合曲线。其他等角线从极点延伸到极点，并环绕每个极点无数次。尽管如此，它们具有由`pi/abs(cos(tc))`给出的有限长度（它使用的是我们的弧度单位，乘以地球半径即可得到距离单位）。

当两个点`(lat1,lon1), (lat2,lon2)`由真航向`tc`的等角线相连：
```
 lon2-lon1=-tan(tc)*(log((1+sin(lat2))/cos(lat2))-
                     log((1+sin(lat1))/cos(lat1))) 
          =-tan(tc)*(log((1+tan(lat2/2))/(1-tan(lat2/2)))-
                     log((1+tan(lat1/2))/(1-tan(lat1/2))))
          =-tan(tc)*(log(tan(lat2/2+pi/4)/tan(lat1/2+pi/4)))
```
（`log`是以`e`为底的“自然”对数。）

点之间的真航向：
```
 tc= mod(atan2(lon1-lon2,log(tan(lat2/2+pi/4)/tan(lat1/2+pi/4))),2*pi)
```
点间距离`d`：
```
         if (abs(lat2-lat1) < sqrt(TOL)){
             q=cos(lat1)
         } else {
             q= (lat2-lat1)/log(tan(lat2/2+pi/4)/tan(lat1/2+pi/4))
         }
         d=sqrt((lat2-lat1)^2+ q^2*(lon2-lon1)^2)
```
如果所讨论的等角线越过`180 E/W`子午线，则此公式将失效。考虑到这一点，连接两个点的最短等角线的真航向`tc`和距离`d`为：
```
  dlon_W=mod(lon2-lon1,2*pi)
  dlon_E=mod(lon1-lon2,2*pi)
  dphi=log(tan(lat2/2+pi/4)/tan(lat1/2+pi/4))
  if (abs(lat2-lat1) < sqrt(TOL)){
     q=cos(lat1)
  } else {
     q= (lat2-lat1)/dphi
  }
  if (dlon_W < dlon_E){// 向西的等角线最短
      tc=mod(atan2(-dlon_W,dphi),2*pi)
      d= sqrt(q^2*dlon_W^2 + (lat2-lat1)^2)
  } else{
      tc=mod(atan2(dlon_E,dphi),2*pi)
      d= sqrt(q^2*dlon_E^2 + (lat2-lat1)^2)
      }
```
要找到`(lat1,lon1)`起（起始点不能为极点！）沿真航向`tc`等角线上距离`d`的某个点纬度/经度：
```
  lat= lat1+d*cos(tc)
    IF (abs(lat) > pi/2) "d太大。你没法沿等角线走这么远！"
    IF (abs(lat-lat1) < sqrt(TOL)){
     q=cos(lat1)
  } ELSE {
     dphi=log(tan(lat/2+pi/4)/tan(lat1/2+pi/4))
     q= (lat-lat1)/dphi
  }
  dlon=-d*sin(tc)/q
  lon=mod(lon1+dlon+pi,2*pi)-pi
```
`TOL` is a small number of order machine precision-例如`1e-15`（译注：这句不知道怎么翻译……）。该测试避免了东-西航线上出现`0/0`的不确定性。

示例：
``` 
  假设点1是洛杉矶国际机场（IATA: LAX）：(北纬 33度 57分, 西经 118度 24分)
  假设点2是纽约肯尼迪机场（IATA: JFK）: (北纬 40度 38分, 西经 73度 47分)
```
从`LAX (0.592539,2.066470)`到`JFK (0.709185,1.287762)`等角线航线：
```
dlon_W=mod(1.287762-2.066470,2*pi)=5.504478
dlon_E=mod(2.066470-1.287762,2*pi)=0.778708

dphi=log(tan(0.709185/2+pi/4)/tan(0.592539/2+pi/4))
    =0.146801
q= (0.709185-0.592539)/0.146801 =0.794586
  dlon_E < dlon_W：向东更近！
tc=mod(atan2(0.778708,0.146801),2*pi)= 1.384464 radians = 79.32 degrees
d=sqrt(0.794586^2*0.778708^2 + (0.709185-0.592539)^2)
= 0.629650 radians = 2164.6 nm
```
将此与航向`66度`及距离`2144 nm`的大圆航线进行比较。

相反，如果我们从LAX在`79.3 degrees (1.384464 radians)`航向等角线行进`2164.6nm (0.629650 radians)`，我们的目标点将为：
```
lat=0.592539 + 0.629650 * cos(1.384464) 
   = 0.709185
dphi=log(tan(0.709185/2+pi/4)/tan(0.592539/2+pi/4))
    =0.146801
q= (0.709185-0.592539)/0.146801 =0.794586
dlon=-0.629650*sin(1.384464)/0.794586=-0.778708
lon=mod(2.066470-0.778708+pi,2*pi)-pi
   =1.287762
```
便是所要求的JFK的纬度/经度。

---
#### 局地地球平面近似
如果你停留在一个给定的固定点`(lat0,lon0)`附近，把地球视为平面是个不错的近似，并使用以固定点为原点的北-东-下直角坐标系。如果我们把纬度和经度变化称为`dlat=lat-lat0, dlon=lon-lon0`（这里把北和东视为正！），那么
```
       distance_North=R1*dlat
       distance_East=R2*cos(lat0)*dlon
```
`R1`和`R2`分别被称为子午线的曲率半径和卯酉圈的曲率半径。
```
      R1=a(1-e^2)/(1-e^2*(sin(lat0))^2)^(3/2)
      R2=a/sqrt(1-e^2*(sin(lat0))^2)
```
`a`是地球赤道半径（对于WGS84`=6378.137000km`），并且`e^2=f*(2-f)`，对于WGS84扁率`f=1/298.257223563`。

在本公式集其他地方使用的球形模型中，地球的半径`R1=R2=R`。（使用`R=1`，我们获得的距离为弧度，使用`R=60*180/pi`的距离为nm。）

在地球平面近似中，距离和方位由通常的平面三角公式给出，即：
```
    distance = sqrt(distance_North^2 + distance_East^2)
    向(lat,lon)的方位角 = mod(atan2(distance_East, distance_North), 2*pi)
                       （= mod(atan2(cos(lat0)*dlon, dlat), 2*pi) 在球面情况下）
```
这些近似在极点附近和长距离情况下无效。相对误差大约是`(distance/R)^2`.

---
#### 风速三角形
（译注：此部分内容暂不感兴趣，先略过）

#### 单位转换等。

```
 1 knot = 1.852000 km/hr*
 1 knot = 185200/109728 ft/sec* =1.687810 ft/sec
 1 knot = 1852000/1609344 mph* = 1.150779 mph
 1 mph  = 0.868976 knot
 1 mph  = 1.609344 km/hr*
 1 mph  = 1.466667 ft/sec
 1 km/hr= 0.539968 knot
 1 km/hr= 0.911344 ft/sec
 1 km/hr= 0.621371 mph
* = 精确转换系数
```
---
椭球参数：
```
名字           长轴, a (km)    扁率 (f)
WGS84          6378.13700	   1/298.257223563
GRS80/NAD83    6378.13700	   1/298.257222101
WGS66          6378.145            1/298.25
GRS67/IAU68    6378.16000          1/298.2472
WGS72          6378.135            1/298.26
Krasovsky      6378.245            1/298.3
Clarke66/NAD27 6378.2064           1/294.9786982138
```
参考资料: *坐标系和地图投影*, D. H. Maling (Pergamon出版社 1992年) （除了Clarke66！）

在地心（半径`r`，地心纬度`u`）和大地坐标（大地纬度`v`，椭球上方的高度`h`）之间转换：
```
  tan(u) = tan(v)*(h*sqrt((a*cos(v))^2+(b*sin(v))^2) +b^2)/
                  (h*sqrt((a*cos(v))^2+(b*sin(v))^2) +a^2)

  r^2 = h^2 + 2*h*sqrt((a*cos(v))^2+(b*sin(v))^2)+
               (a^4-(a^4-b^4)*(sin(v))^2)/(a^2-(a^2-b^2)*(sin(v))^2)
```
`a`和`b`是椭球的半长轴，且`b=a*(1-f)`，其中`f`是扁率。注意，地心经度和大地经度相等。

---
#### Turns and pivotal altitude
（译注：此部分内容暂不感兴趣，先略过）

---
评论、更正和建议请发至：

> Ed Williams  
> [点此获得地址](http://edwilliams.org/contact.html)