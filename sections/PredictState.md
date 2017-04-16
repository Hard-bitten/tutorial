The purpose of this class is to predict some game variables in a specific state according to a player's world model.

For example:
- all relative to the ball (rcsc :: ballObject)
- Game Time (rcsc :: GameTime)
- Game Mode (rcsc :: GameMode)
- field side (rcsc :: SideID)

Some important methods:

```cpp
const rcsc::AbstractPlayerObject * ballHolder() const  // Checks who has the ball, opponent or teammate, and returns the player's reference
const rcsc::AbstractPlayerObject & self() const        // Verifies that the player to be interacted is not the same one with the ball
```

