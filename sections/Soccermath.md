```cpp
/ *!
   \ Brief Calculates the kick rate
   \ Param dist Player's distances to the ball
   \ Param dir_diff Difference between the angle of the player and the ball
   \ Param kprate Player kicking force rating
   \ Param bsize Ball radius
   \ Param psize Player radius
   \ Param kmargin Margin of player's kicking area
   \ Return Kick force effect rate

   It may be useful to redefine this algorithm in the kick module
* /
inline double kick_rate( const double & dist,
           const double & dir_diff,
           const double & kprate,
           const double & bsize,
           const double & psize,
           const double & kmargin )
```