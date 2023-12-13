# Dev Blog 1 - 12/11/2023

## Game AI Final Blog Post - My Perspective


## Introduction
For the past couple of weeks Ben Stone ([@Flowslikelightning](https://github.com/FlowLikeLightning)), and myself have been working on An AI buddy that utilizes a Flocking Behavioral Technique and Astar search algorithm to create a AI buddy that follows the player and takes direction from player input. The project so far has been difficult at times and enjoyable at others, as of writing this, I can confidently say we have created a good Prototype that has potential for being well optimized and built upon in the future to create a robust AI companion. It is important to note this project was developed in a Unity project and in the C# programming language.

## Techniques
Here are Explainations of the techniques used in our project, Flocking will be covered in a further depth since it was my main focus while providing support to Ben with the much more difficult Astar.

### Flocking Behavior

The Flocking behavior implemented in this project is based on the general flocking behavioral seen in animals that move in large groups such as fish and migratory birds. The behavior has 3 main components:

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


### Astar Algorithm