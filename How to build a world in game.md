<center>How to build a world in game</center>

+ Game Object (GO)

  + dynamic game objects
  + static game object
  + sky
  + vegetation
  + terrain
  + other game objects

+  多继承的问题的解决方法 -- component base

  + 多用组件，少用继承

  

+ Takeways
  + Everything is a game object in the game world
  + Game object could be described in the component-based way
+ move
  + traditional way: tick every object
  + modern: tick every system
  +  
+ Events, 观察者模式
  + message sending and handling
  + decoupling event sending and handling

+ Manage GO
  + Scene Management
    + Game objects are managed in a scene
    + Game object query
      + By unique game object ID
      + By object position 

+ core
  + everything is an object
  + game object should be component-based 
  + states of go are updated in tick loops
  + go interact by event mechanism
  + go managed in a scene with efficient strategies

