In this section the SBSP (Situation Based Strategic Positioning) training system is described, or something like Strategic Situation Based on Situation. In this SBSP, each player agent is not considered in the movement of the other players and for the target position of the movement is considered as input only the position of the ball. This is done to seem only that it is not a co-operative positioning, but rather as a seemingly individual co-ordination.
The positioning behavior described in this section is something close to the individual abilities of each player agent. However, since adjustments are required in consideration of the entire team's balance in implementing the operation, what follows here is a part of the Team Development section.

### Marking the opposing player ###

Marking next to a specific opponent is required when the team is on its defensive field. Obviously, this operation can be done in other situations, but this can lead to excessive use of the player's stamina. However, this does not necessarily lead to a dangerous situation of play that could disrupt the formation of the team. In the opponent's field and without a ball, a distance marking is enough.
With possession of a ball, a defense marking can cause problems in trying to receive the ball in a pass; However, in its field of attack and being without the ball, this will be the main objective of this behavior. In order to achieve this, more than marking the desired opponent it is necessary to move close to the position of the ball.
Before deciding the target position of the marking move, it is first necessary to determine which opponent player will be scored. In the following code, it is decided which opponent player will be the candidate:

```cpp
const PlayerObject * getMarkTarget( const WorldModel & wm, const Vector2D & home_pos ) 
{
	double dist_opp_to_home = 1000.0;
	const PlayerObject * opp = wm.getOpponentNearestTo( M_home_pos, 1, &dist_opp_to_home );

	if ( ! opp || ( wm.existKickableTeamamte() && opp->distFromBall() > 2.5 ) ) {
		return NULL;
	}
	if ( dist_opp_to_home > 7.0 && home_pos.x < opp->pos().x ) {
		return NULL;
	}
	const PlayerPtrCont::const_iterator
	end = wm.getTeammatesFromSelf().end();

	for ( PlayerPtrCont::const_iterator it = wm.getTeammatesFromSelf().begin(); it != end; ++it ) {
	    if ( (*it)->pos().dist( opp->pos() ) < dist_opp_to_home ) {
	        return NULL;
            }
	}
	return opp;
}
```
The above code determines the position to which the player agent must perform the marking move on the opponent, which consequently becomes the target player.

By getting a pointer to the opponent's PlayerObject in the getMarkTarget method, a last modification in the Y coordinate is required to roll back without requiring any extra rotation. Here, the value of the Y position of the agent player itself is used, but you can use a fixed value.

```cpp
AngleDeg block_angle = ( wm.ball().pos() - opp->pos() ).th();
Vector2D block_point = opp->pos() + Vector2D::polar2vector( 0.2, block_angle );
block_point.x -= 0.1;

if ( block_point.x < wm.self().pos().x - 1.5 ) {
    block_point.y = wm.self().pos().y;
}
```

In the code snippet above, the goal is to score the opponent close to the player agent's home position. In a more comprehensive view where a balance between the role of each player, the formation of the team and the other teammates, would require a more complex marking. However, when it comes to rule-making, it is difficult to mark the opponent as difficult, particularly when information for identifying other players is insufficient.
Therefore, to achieve more reliable markup behavior, sharing information through communication between players is essential.

### Lock the pass line ###

Blocking the pass line is an important defensive tactic because it reduces the options of the opposing team. However, since the player with the ball takes the initiative, path behavior does not allow the path course to be blocked even if a player with well-balanced characteristics is used to attempt blocking. Also, as the ball moves quickly, it is not always necessary to try to block the pass line as it will be difficult to follow the movement of the ball. Consequently, similar to marking behavior, it is best to try to block the pass line in defensive situations where the team is in its defensive field.


The following code shows how to lock the pass line from the front of the goal to the direction of the corner.

```cpp
const BallObject & ball = agent->world().ball();
Vector2D center( -41.5, 0.0 );

if ( ball.pos().x < -45.0 ) {
    center.x = std::max( -48.0, ball.pos().x + 1.0 );
}

Line2D block_line( wm.ball().pos(), ( center - wm.ball().pos() ).th() );
Vector2D block_point;
block_point.y = std::max( 15.0, ball.pos().absY() - 10.0 );

if ( ball.pos().y < 0.0 ) block_point.y *= -1.0;
block_point.x = block_line.getX( block_point.y );
```

In the example above, the player agent was assigned to move in a straight line between a fixed position in front of the goal center and the position of the ball. At 10m from the ball, the minimum distance from the Y-axis is 15m from the Y-coordinate of the block_point. Note that the role of the player must adopt other rules when the value of the Y coordinate of the ball position is 15m or less. The X coordinate can be calculated from the equation of the line and the value of the Y coordinate.
This implementation can not be considered optimal because it uses a magic number, as well as similar implementations.

If you wish to block the pass line of particular opponents, the player agent must move in the straight line that connects the opponent and the ball. However, the target position of the movement will change during the cycles considering that the ball and the opponent will move.
During the lock, determining whether the position of the move is good in a fixed way will bring more results than just trying to block a straight line from any of the pass.

### Marking in front of opponent ###

Imagine a situation where the opposing player initiates a dribble. You can not just run after him and try to steal the ball by performing a behavior to intercept the dribble.
In this case, there must be a marking in front of the opponent.

Follow the code:

```cpp
const WorldModel & wm = agent->world();
const PlayerObject * opp = wm.interceptTable()->fastestOpponent();

if ( ! opp ) {
    return;
}
Vector2D block_point = opp->pos();

if ( wm.self().pos().x > opp->pos().x ) {
    block_point.x -= 5.0;
} else {
    block_point.x -= 2.0;
}

Body_GoToPoint( block_point,
     1.0, // 1m para o erro da distância
     ServerParam::i().maxPower(),
     5, // de modo a atingir a posição de destino depois de cinco ciclos
     false, // não usar dash para trás
     true, // não consumir recover
     40.0 //  permitir até 40 graus de erro de direção 
).execute( agent );
```

You must be imagining that the code above to get the target position is pretty simple. In fact, it's quite simple. However, the intention is not to go exactly at the point where the opponent's movement was predicted.
What's more, the above code can result in unnecessary rotations when drawn to just one point. To resolve this, simply assign a larger error value in the BodyGoToPoint method. As a result, you can reduce unnecessary rotations by only slightly changing the direction of the target position.

Predicting opponent behavior can be more effective when executed by offensive players.

### Movement to cancel the dialing ###

1. Dynamic search

There are several methods for calculating the potential field as a way of changing the position of the motion. The following code gets the density of the player in a certain position.


```cpp
Vector2D target_point( 45.0, 0.0 );
double congestion = 0.0;
const PlayerPtrCont::const_iterator end = agent->world().getOpponentsFromSelf().end();

for ( PlayerPtrCont::const_iterator o = agent->world().getOpponentsFromSelf().begin(); o != opps_end; ++o ) {
    congestion += 1.0 / (*o)->pos().dist2( target_point );
}
```

In the above example, the congestion density of the opposing players is obtained at a specific point (target_point). The position that the congestion value has is minimized according to the calculation of several position coordinates measured from the target_point.

Everything looks great on this algorithm, but it does not work in that case. The field of view of the player agent is limited and since each player is moving, the target positions of the opposing players are constantly changing. In this case, rotation operations included several player agent moves and this would not be efficient at all.

One solution to this is to record the intention to move the timeout in a way that continues to perform move operations to the position, so in a certain period of time you can decide which target position will be chosen according to the time considered.

However, even by reducing movements using intention, dynamic changes of the target position that is in motion does not seem to have much effect. If you want a more reliable effect, it is best to use a method that incorporates static rules that will be described below.


```cpp
const WorldModel & wm = agent->world();
Vector2D target_point = home_pos;

if ( wm.ball().pos().y * wm.self().pos().y < 0.0 && wm.ball().pos().x > 40.0 
       && 6.0 < wm.ball().pos().absY() && wm.ball().pos().absY() < 17.0 ) {

    Rect2D goal_area( Vector2D( 52.5 - 8.0, -6.0 ),
    Vector2D( 52.5, 6.0 ) );

    if ( ! wm.existTeammateIn( goal_area, 10, true ) ) {
        target_point.x = wm.ball().pos().x + 2.0;
        
        if ( target_point.x > wm.getOffsideLineX() - 1.0 ) {
            target_point.x = wm.getOffsideLineX() - 1.0;
        }
    
        if ( target_point.x > 49.0 ) target_point.x = 49.0;
        target_point.y = -1.0;
        if ( wm.ball().pos().y > 0.0 ) target_point.y *= -1.0;
    }
}
```

You can also perform the operation to cancel the markup via fixed positioning rules. In this way, it is possible to determine the fixed position of the target point. To be more precise, this is not a dynamic positioning, this is a tactical positioning for a local situation, as will be explained in more detail in the next section.
For example, in the case of the ForwardSide role, what you should do is move to the front of the goal to score the opponent forward as shown in the following code.

```cpp
const WorldModel & wm = agent->world();
Vector2D target_point = home_pos;

if ( wm.ball().pos().y * wm.self().pos().y < 0.0 && wm.ball().pos().x > 40.0
       && 6.0 < wm.ball().pos().absY() && wm.ball().pos().absY() < 17.0 ) {

	Rect2D goal_area( Vector2D( 52.5 - 8.0, -6.0 ),
	Vector2D( 52.5, 6.0 ) );

	if ( ! wm.existTeammateIn( goal_area, 10, true ) ) {
		target_point.x = wm.ball().pos().x + 2.0;
		if ( target_point.x > wm.getOffsideLineX() - 1.0 ) {
		    target_point.x = wm.getOffsideLineX() - 1.0;
		}
		if ( target_point.x > 49.0 ) target_point.x = 49.0;
		target_point.y = -1.0;
		if ( wm.ball().pos().y > 0.0 ) target_point.y *= -1.0;
	}
}
```

### Player agent body orientation adjustment ###

The best idea during the game is to adjust the orientation of the body even when there is no action to be taken, particularly when the player has already reached the final position of his movement.
For an offensive player, it is enough to just turn the body towards the direction of the opponent's goal line. This can be easily obtained with the following code:

```cpp
if ( ! Body_GoToPoint( target_point, dist_thr, dash_power).execute( agent ) ) {
    Vector2D face_point( 100.0, wm.self().pos().y );
    Body_TurnToPoint( face_point ).execute( agent );
}
```
In addition, it is useful to adjust the position through the rear dash without having to change the orientation of the body when a player with a more aggressive marking role has to move continuously to the target position.

```cpp
if ( wm.self().pos().x > target_point.x + dist_thr*0.5 
    && std::fabs( wm.self().pos().x - target_point.x ) < 3.0 && wm.self().body().abs() < 10.0 ) {

    double back_dash_power = wm.self().getSafetyDashPower( -dash_power );
    agent->doDash( back_dash_power );
}
```

For a player with a defensive role, it is necessary to elaborate almost a "rational behavior" of interception. For example, in the case of a role that makes a defensive line the interception, considering that it is necessary the movement that interferes the course of movement of the opponent player side by side, is shown in the following code:

```cpp
if ( ! Body_GoToPoint( target_point, dist_thr, dash_power ).execute( agent ) ) {
    Vector2D face_point( wm.self().pos().x, 0.0 );
    face_point.y = ( wm.ball().pos().y < wm.self().pos().y ? -40.0 : 40.0 );
    Body_TurnToPoint( body_point ).execute( agent );
}
```

In addition, you will need to store the perpendicular direction of the body of the player agent relative to the ball so that it will be easy to handle if it is suddenly kicked:

```cpp

if ( ! Body_GoToPoint( target_point, dist_thr, dash_power ).execute( agent ) ) {
    AngleDeg body_angle = wm.ball().angleFromSelf();
   
    if ( body_angle.degree() < 0.0 ) body_angle -= 90.0;
    else body_angle += 90.0;
  
    Vector2D face_point = wm.self().pos();
    face_point += Vector2D::polar2vector( 10.0, body_angle );
    Body_TurnToPoint( face_point ).execute( agent );
}
```

However, you should check the position of the ball in any case. Thus, the operation to adjust the orientation of the body must be performed only when it is possible to confirm the position of the ball.