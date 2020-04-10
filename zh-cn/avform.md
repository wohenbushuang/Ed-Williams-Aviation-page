# 航空公式集 V1.46
### 原作者 Ed Williams

#### 目录
- [大圆法导航公式概述](#introduction)
- [大圆法导航公式](#some-great-circle-formulae)
  - [点间距离](#distance-between-points)
  - [点间航线](#course-between-points)
  - [大圆上点的纬度](#latitude-of-point-on-gc)
  - [Lat/lon given radial and distance](#latlon-given-radial-and-distance)
  - [Intersection of two radials](#intersecting-radials)
  - [克莱劳公式](#clairauts-formula)
  - [GC crossing parallel](#crossing-parallels)
  - [大圆中间的点](#intermediate-points-on-a-great-circle)
  - [Cross track error and along track distance](#cross-track-error)
  - [距离大圆已知距离的点](#points-known-distance-from-a-great-circle)
- [Implementation notes实现说明](#implementation-notes)
  - [Atan, atan2, acos, asin 和 mod 函数](#notes-on-mathematical-functions)
  - [符号约定](#sign-convention)
- [Worked Examples](#worked-examples)
- [Some general spherical triangle formulae.](#some-general-spherical-triangle-formulae)
- [`Right spherical triangles`](#right-spherical-triangles)
- [等角航法导航](#rhumb-line-navigation)
- [局地地球平面近似](#local-flat-earth-approximation)
- [Wind Triangles](#wind-triangles)
- [Head and cross-winds](#head--and-cross-wind-components)
- [TAS and wind speed from three groundspeeds](#tas-and-windspeed-from-three-gps-groundspeeds)
- [Variation](#approximate-variation-formulae)
- [Altimetry and the standard atmosphere formulae](#standard-atmosphere-and-altimetry)
- [Mach numbers, true vs calibrated airspeeds etc.](#mach-numbers-true-vs-calibrated-airspeeds-etc)
- [Relative humidity related to temperature and dewpoint or frostpoint](#relative-humidity-dewpoint-frostpoint-etc)
- [Bellamy's formula for the wind drift](#bellamys-formula)
- [Unit conversions, etc.](#unit-conversions-etc)
- [Turns and pivotal altitude.](#turns-and-pivotal-altitude)
- [Distance to the horizon.](#distance-to-horizon)
- [Revision History](#revision-history)

[Javascript calculator with elliptical earth models](http://edwilliams.org/gccalc.htm)

[Sunrise-sunset algorithm](http://edwilliams.org/sunrise_sunset_algorithm.htm)

[Sunrise-sunset example](http://edwilliams.org/sunrise_sunset_example.htm)

[Compass errors](http://edwilliams.org/compass/compass.html)

[Navigation on spheroidal earth](http://edwilliams.org/ellipsoid/ellipsoid.html)

---

#### 概述