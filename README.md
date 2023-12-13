# Dev Blog 1 - 12/11/2023

## Game AI Final Blog Post - My Perspective


## Introduction
For the past couple of weeks Ben Stone ([@Flowslikelightning](https://github.com/FlowLikeLightning)), and myself have been working on An AI buddy that utilizes a Flocking Behavioral Technique and Astar search algorithm to create a AI buddy that follows the player and takes direction from player input. The project so far has been difficult at times and enjoyable at others, as of writing this, I can confidently say we have created a good Prototype that has potential for being well optimized and built upon in the future to create a robust AI companion. It is important to note this project was developed in a Unity project and in the C# programming language.

## Techniques
Here are Explainations of the techniques used in our project, Flocking will be covered in a further depth since it was my main focus, although I worked with Ben on parts of our Astar Implementation as well.

### Flocking Behavior

The Flocking behavior implemented in this project is based on the general flocking behavior seen in animals that move in large groups such as fish and migratory birds. We took a lot of inspiration from [Alexandre Tolstenko's AI Course Post](https://courses.tolstenko.net/courses/artificialintelligence/assignments/flocking/) on Flocking, with our implementation utilizing the 3 general force components seen in other versions by other developers.
These 3 components being:

> #### Alignment Force

Alignment Force is the component which calculates the directional velocity to move in the same direction as other agents (other) in a "neighborhood" (group of agents). Of the 3 components it is often the easiest to implement. First you combine all the velocities of the agents, divide by the number of agents in the neighbor hood, normalize the force to a unit measure and that is the alignment force.

In our implementation with a single buddy, we simply get velocity of the player because it is the only neighbor and apply it based on neighborhood size plus an additional count of 1 to account for the buddy being apart of the neighborhood.

```c#
private void AllignmentCalc(Collider col)
    {
        allignmentForce += player.GetComponent<Rigidbody>().velocity;
        allignmentForce /= neighborhood.Count + 1;
        allignmentForce = allignmentForce.normalized;
    }
```

> #### Cohesion Force

Cohesion is similar to Alignment force, however rather than focus on keeping agents in a uniform direction, the use of cohesion force is to keep the agents in a group by applying a force that pushes them towards the center of the group which is calculated by iterating through the neighborhood, adding together all the positions, <code>posSum += neighborhood[i].transform.position;</code> and then dividing that result by the number of agents in the neighborhood <code>PosCenter = posSum / (neighborhood.Count);</code>. You then take the center position vector and subtract it from the current agent's position to create a directional vector towards the center position. Again this value is normalized to create a unit measure and the cohesion force is updated to this value.

> #### Seperation Force

 Seperation Force maintains the distances between agents in a neighborhood, correcting them if they get too close to one another. This is accomplished by calculating a distance vector between the current agent and a neighbor <code>Vector3 diffVect = transform.position - neighborPos;</code>, iterating through the neighborhood and checking if each agent within a certain radius of the current agent. you then iterate through each agent that is within the radius, normalize the distance vector and divide by the magnitude of the intial distance vector to create the seperation force. lastly you divide this force by the number of agents within the radius to balance the force applied.
 
 In most implementations you'd find that seperation force is clamped between a value of 0 and 1 however because we update the force continuously in unity and given the fact we only have one buddy, the clamping was not necessary considering the values were already low, if we scale up to about 10 buddies it may be a necessary to maintain distance between the buddies but, the player often outpaces the buddy, not to mention we've added player velocity to the checks and stop the buddy when the player is not moving.

### Astar Algorithm

Very obviously if you have an AI buddy in a game you will want to have it be useful or interactable, in our project we went for a command approach, our main focus was on being able to tell the buddy where in the level it should go. This is why we chose to implement Astar as the AI's navigation in order to minimize the load of the AI's movements on the system.

>IMPORTANT NOTE: When dealing with 3D continous spaces such as Unity 3D, the concept of "Spacial Quantization" is needed in order to quantize the continous space to be able to create paths for navigational algorithms such as Astar and others. It wont be covered here as it is not the main focus of the project however we took a lot of inspiration from [Alexandre Tolstenko's Blog Post](https://courses.tolstenko.net/courses/artificialintelligence/readings/spatial-quantization/) on Spatial Quantization to help build our Astar.

Astar itself is an amazing navigational algorithm that utilizes priority queues and heuristics to create the most efficient paths, [RedBlobGames](https://www.redblobgames.com/pathfinding/a-star/introduction.html) has a very good introduction to Astar and other search algorithms that we utilized to understand the technique so we could then make our version.

necessary parts include:

- A frontier - used to iterate through each node.
- A camefrom array - used to collect the best neighbors for a given path.
- visited array - used to mark all neighbors not being used.

The general step by step of Astar is as follows:
(Each component is being quantized and dequantized as necessary).

 1. Add start position to frontier.
 2. Get available neighbor positions of start position.
 3. Add available neighbors to frontier.
 4. Compare distance costs.
 ```c#
 Vector3Int minPos = new Vector3Int();
float minDist = float.MaxValue;
foreach (var pos in frontier)
{
    if (distKeeper.TryGetValue(pos,out float dist)&&dist<minDist)
    {
        minPos = pos;
        minDist = dist;
    }
}
return minPos;
 ```
 5. Added best neighbor to camefrom array.
 6. Mark start and neighbors as visited, and move frontier to best neighbor.
 7. Repeat until goal position is added.
 8. Organize camefrom array and return the path needed.

 This is a very basic explination of a general Astar algorithm, in our implementation we utilized a manhattan distance between the goal position picked by the player, and kept track of the distance compared to the manhattan distance as part of our heuristic to added the best neighbor postions to create the path. so the process in a Unity Update Function is to get the position clicked by the player, create a path, a direction vector is then calculated based on the path, and force is added to the AI buddy and velocity pushes the buddy towards the selected point.

## Values and Suitable Platforms

The values of these techniques lies in their functionality, for games like League of Legends, and DOTA, Flocking and Astar algorithms are used by the Minion AI in order to navigate and attack the nearest Towers and Defenses. When utilized in contexts such as ours, where the player controls the AI to an extent, these Techniques when built and optimized to the fullest can provide seamless and smooth feedback to the player and create the sense of the control that the developer wants to provide to players in the game, that can be tuned based on the game genre.

Most platforms like desktops, laptops, and consoles can handle these techniques well, even most phones, the general downsides to these techniques are:

- Depth of implementation - The more limitations, and logic checks these techniques perform can severely limit performace on platforms with less bandwith like phones, tablets, and consoles.
- Scale - This goes hand in hand with Depth of implementation since the number of things being affected by these calculations can use a lot of memory and space during high usage times.
- Optimization - If the calculations and functions are not optimized they can easily bog down the performance of the platform with only a couple agents interacting, this is why optimization is important in later stages especially in games with so many other systems working at the same time.


## Check out the Demo Build

(Very much a Prototype but take a look if interested)

<a href="Unity Build.zip" download>Click here to download</a>
