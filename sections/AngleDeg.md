**rcsc/geom/angle_deg.h**

A class that represents angles in degrees. As is well known, the mathematical functions present in ANSI C are for radians only, so the use of this class is extremely important in many calculations during a game.

<br>
<p align="center">
<img src="https://raw.githubusercontent.com/robocup2d/robocup2d/master/images/polar_coordinate_system.png" widht="150" height="150" style:"align: center" />
</p>

In the image above, if we consider the point _P_ to be _P (4, 3) we will then have the value of the angle φ equal to:
```matlab
r² = 4² + 3²
r = sqrt(25)
r = 5

sin φ = 25 / r
sin φ = 25 / 5
φ ≃ 2.78 rad ou φ ≃ -55.59º
```

Useful methods of this class:
```cpp
const AngleDeg & normalize()   // normalize the angle in the interval [-180 °, 180 °]
double abs() const             // returns the absolute value of the angle
double radian() const          // returns the angle value in radians
```
This class has many other useful methods like cosine, sine, tangent, arc-tangent, among others in degrees and radians, in addition to arithmetic operators overhead,