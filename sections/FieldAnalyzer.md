Probably the most important class to understand the operation of agent2D. It is she who will literally analyze the field from the Voronoi diagram and then decide what action should be taken by the agent.

A very simple explanation of the Voronoi diagram can be found here:
Http://www.ime.usp.br/~freitas/gc/voronoi.html

To understand the operation of the librcsc Voronoi diagram code, see the [Voronoi Diagram] class (https://github.com/RoboCup2D/tutorial/blob/master/sections/VoronoiDiagram.md).

In the [SamplePlayer] actionImpl () method (https://github.com/RoboCup2D/tutorial/blob/master/sections/SamplePlayer.md) (which is called every cycle of the game), the instance of the FieldAnalyzer class is called Already in the second row. Before it, the call responsible for the game strategy also updated. (For more details, see the [Strategy] class (https://github.com/RoboCup2D/tutorial/blob/master/sections/Strategy.md)).

```cpp
void
SamplePlayer::actionImpl()
{
    //
    // update strategy and analyzer
    //
    Strategy::instance().update( world() );
    FieldAnalyzer::instance().update( world() );

   //[...]
```
Before the chain of actions is generated, the field is analyzed with each cycle. Note that at this point, it is very important that you have understood the Voronoi diagram.

The call made in the SamplePlayer has the following code in the FieldAnalyzer class:
```cpp
void
FieldAnalyzer::update( const WorldModel & wm )
{
    static GameTime s_update_time( 0, 0 );

    if ( s_update_time == wm.time() )
    {
        return;
    }
    s_update_time = wm.time();

    if ( wm.gameMode().type() == GameMode::BeforeKickOff
         || wm.gameMode().type() == GameMode::AfterGoal_
         || wm.gameMode().isPenaltyKickMode() )
    {
        return;
    }
    
#ifdef DEBUG_PRINT
    Timer timer;
#endif

    //updateVoronoiDiagram( wm );

#ifdef DEBUG_PRINT
    dlog.addText( Logger::TEAM,
                  "FieldAnalyzer::update() elapsed %f [ms]",
                  timer.elapsedReal() );
    writeDebugLog();
#endif
```
The excerpt of the code where the method that actually updates the Voronoi diagram is called in the original agent2D code. ** Tests need to be done to analyze the impact of this method **.
Following the rest of the logic, this method basically excludes some situations where the diagram is not updated, they are beginning and resuming of the game and in the following game modes: BeforeKickOff, AfterGoal_ and isPenaltyKickMode.
