Class representing actions to be taken in group by an agent.

The categories of actions are:

Hold - hold the ball
Dribble - dribbling the opponent
Pass - pass the ball to the teammate
Shoot - kick the ball towards the goal
Move - move
NoAction - no action

Basically, the class has get and set methods to store information such as player number, action category, action end point, etc.

The class also has a simple struct and a method for comparing distances between two points.

```cpp 
DistCompare( const rcsc::Vector2D & pos )
            : pos_( pos )
          { }

        bool operator()( const Ptr & lhs,
                         const Ptr & rhs ) const
          {
              return lhs->targetPoint().dist2( pos_ ) < rhs->targetPoint().dist2( pos_ );
          }
```
Operator () overloaded to pass two shared_ptr parameters of the CooperativeAction class type itself.
This function is used to compare two instances of the CooperativeAction class and determine which is the greater or lesser value of the Euclidean distances between the vector2D targetPoint and the point passed as a parameter in the DistCompare constructor method of the struct of the same name. (** See: ** dist2 in [Vector2D] (https://github.com/RoboCup2D/tutorial/blob/master/sections/Vector2D.md))