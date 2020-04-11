[Original page](http://edwilliams.org/intersect.htm) | [中文翻译版](../zh-cn/intersect.md)

To find the intersection of two great circles defined by the arcs from `pt1 (lat1, lon1)` to `pt2` and from `pt3` to `pt4`.

-----------------

Use the "`W` longitude is positive" convention of the Aviation Formulary. (For the more conventional convention, change the sign of all longitudes in the following.)

For each point we can associate a unit vector pointing to it from the center of the earth whose components are:
```
e ={ex,ey,ez} = {cos(lat)*cos(lon), -cos(lat)*sin(lon), sin(lat)} (1)
```
which we can invert with
```
lat=atan2(ez, sqrt(ex^2 + ey^2)); lon=atan2(-ey, ex) (2)
```
For any great circle, defined by pts 1 and 2, the point
```
P(e1,e2) =(e1 X e2)/||e1 X e2||
```
is perpendicular the the plane of the circle. (Its negative is the opposite point).

Here `e1 X e2` is the vector cross-product whose components are
```
{e1y *e2z -e2y *e1z, e1z *e2x -e2z *e1x, e1x *e2y -e1y *e2x} (3)
```
respectively (see later for a numerically robust way to compute this!)

`||e||` is the length of a vector defined by
```
||e|| =sqrt(ex^2 + ey^2 + ez^2) (4)
```
The intersections of the great circles can be seen to be given by
```
+- P( P(e1,e2), P(e3,e4) )
```
Direct computation of the cross-product will fail at small distances because of rounding error. Application of some trig identities gives:
```
e1 X e2 = { sin(lat1-lat2) *sin((lon1+lon2)/2) *cos((lon1-lon2)/2) -

sin(lat1+lat2) *cos((lon1+lon2)/2) *sin((lon1-lon2)/2) ,

sin(lat1-lat2) *cos((lon1+lon2)/2) *cos((lon1-lon2)/2) +

sin(lat1+lat2) *sin((lon1+lon2)/2) *sin((lon1-lon2)/2) ,

cos(lat1)*cos(lat2)*sin(lon1-lon2) } (5)
```
which avoids this problem.

# Algorithm:

compute `e1 X e2` and `e3 X e4` using (5).

Normalize `ea= (e1 X e2)/ ||e1 X e2||` , `eb=(e3 x e4)/||e3 X e4||` using (4)

Compute `ea X eb` using (3)

Invert using (2) (it's unnecessary to normalize first).

The two candidate intersections are `(lat,lon)` and the antipodal point `(-lat, lon+pi)`

 