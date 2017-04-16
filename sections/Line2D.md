**rcsc/geom/line_2d.h**

Class representing the linear formula aX + bY + c = 0.
Too much to calculate trajectories of players or the ball and to check if a point or a line intercepts another line.

The image below shows a line in the two-dimensional plane and a point perpendicular to it:

! [Line2d.png] (https://github.com/RoboCup2D/tutorial/raw/master/images/line2d.png)

Useful methods of this class:
```cpp
double dist( const Vector2D & p ) const         //Calculates a Euclidean distance from a point to this line
bool isParallel( const Line2D & line ) const    // Check if a line is parallel to this line
Vector2D intersection( const Line2D & line ) const     // Returns the point of intersection of the line with another
Line2D perpendicular( const Vector2D & p ) const       // Returns the line perpendicular to the other from a specific point
static Vector2D intersection( const Line2D & line1, const Line2D & line2 );   // returns the point of 
                                                                              // intersection of two lines
static Line2D angle_bisector( const Vector2D & origin, const AngleDeg & left, const AngleDeg & right )
                                                                        // Returns the bisector of an angle
```

**References**:
Bisector Angle - http://mathworld.wolfram.com/AngleBisector.html
Equation of the line - http://mathworld.wolfram.com/LinearEquation.html
