This class reads the team formations and also defines each player's [_role_] (https://github.com/RoboCup2D/tutorial/blob/master/sections/Roles.md) according to the formation.

First, it is necessary to understand the identification of the area where the ball is in the field. In the Strategy class, there is an _enum_ to identify such areas:

```cpp
enum BallArea {
        BA_CrossBlock, BA_DribbleBlock, BA_DribbleAttack, BA_Cross,
        BA_Stopper,    BA_DefMidField,  BA_OffMidField,   BA_ShootChance,
        BA_Danger,

        BA_None
    };
```
According to the figure below, one can better understand this division:

! [Ball-area.png] (https://github.com/RoboCup2D/tutorial/raw/master/images/ball-area.png)

These demarcations can be changed later according to the needs of the team.
There are two methods to return in which area the ball is: one with the position of the ball as a parameter and another with the world view of the player as a parameter (this second method calls the first).

```cpp
 static BallArea get_ball_area( const rcsc::WorldModel & wm );
 static BallArea get_ball_area( const rcsc::Vector2D & ball_pos );
```
