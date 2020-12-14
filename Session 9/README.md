# Session 9 - Collision Response

#### Table of Contents
1. [New concepts for collision response](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%209#new-concepts-for-collision-response)
2. [The most basic collision response](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%209/README.md#the-most-basic-collision-response)
3. [Point masses](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%209#point-masses)
4. [Interpenetration](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%209#interpenetration)
5. [Rigid Bodies](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%209#rigid-bodies)
6. [Homework](https://github.coventry.ac.uk/217CR-2021/Teaching-Material/tree/master/Session%209#homework)

We have physics objects moving around the environment and detecting collisions. However, the objects still just move through each other without any interaction. Pretty much all games need objects to interact with one another when they collide.

This session will discuss collision response (or collision resolution).

## New concepts for collision response

In this week's lecture we learned about:

| Topic | Description |
| ------------- | ------------- |
| Co-efficient of restitution | A value between 0 and 1 that allows us to incorporate loss of kinetic energy during a collision |
| Momentum | Mass in motion aka mass * velocity |
| Conservation of momentum | The concept that energy is not lost or gained during a frictionless collision |
| Impulse | A force applied over a short time period. This is the change of momentum over time | 

All of the above concepts allowed us to derive (or obtain) both a formula for working out the impulse needed to seperate 2 objects in response to a collision, and how to apply this impulse to each object's velocity to instantly change their velocities.

## The most basic collision response

Some older games used a concept even easier than the ones discussed in the lecture slides.

They simply reversed the velocity of the game object when it hit something. While this results in objects moving away from each other (if they were moving towards each other before the collision), this method gives unrealistic results.

They might also just set the velocity of the object hitting something to zero to make it completely stop. This might have been used for objects hitting the terrain/ground so they do not move elsewhere.

## Point masses

For point masses (or particles), we only can update the linear motion with collision resolution and the impulses it creates.

Currently in our game engine, we update every frame in the _GameEngine_ class. In this, we will calculate delta time, collision detect and then update each object in a for loop, based on deltaTime.

We will have to update our code in a few areas to include collision response.

> We will need to resolve the collisions between the "collision detection" and "update each object" phase.

> If you haven't already, make a _vector_ that holds some collision information that should include both objects, the collision normal and collision interpenetration depth. (This was in the last online tutorial if you have skipped ahead!)

What we will need to do is:

* Go through the collision information _vector_ which holds all the collisions that have happened.
* Gather all the needed information from both objects for the impulse formula. (This is on the slides)
	* You may need to add some getters and setters to the _GameObject_ class so you can see the needed variables you need (if they are private).
	* Depending on how you have coded it, you may need to either cast the game objects to get access to things like rigid body variables, or have empty functions for the _GameObject_ class that only do things for a point mass or a rigid body (like getting the angular variables).
* Calculate the impulse force from the collision via the impulse formula.
* Apply this impulse to each object's velocity taking into account the mass of the object and the current object's velocity (Again, this is on the slides).

After this, the _Update_ function for each object will use the current velocity (which we have now changed via an impulse) to calculate the new position of the object. Done correctly, the object will bounce off the object in the correct direction and bounce amount.

## Interpenetration

If the objects are going very fast, this means that they can move a lot between frames. The issue here is that an object may be outside an object in one frame and then inside, or interpenetrating, an object in the next frame.

We've all saw this in games we play - A game object penetrates another game object causing it to either be stuck in geometry or oscillate wildy as the physics engine tries to solve it.

> A quick example - https://thumbs.gfycat.com/DisguisedAdventurousFowl-size_restricted.gif - Notice the wheels on the landing.

The best case for this is that the physics engine can solve it and the break in immersion is low. The worst case is that an object gets stuck and its game breaking.

> _Or we can have a good laugh due to objects getting stuck..._ - https://www.youtube.com/watch?v=x-6OC1MyplU 

While this might not be a major problem for your game engine, we might as well add a way to somewhat solve interpenetration. Only by testing object collisions and resolutions for your personal implementation of physics and the objects in the scene will you know if interpenetration happens. (Your games are updating rapidly with relatively small movement between frames for example)

The fix would be to move the objects in the collision away from each other based on the collision information before the impulse is calculated during the Collision Resolution phase.

> What information from the collision detection phase would be useful for this task?

<br/>
<details>
  <summary> Useful Information </summary>
Both the collision interpenetration depth and the collision normal are needed here.
</details>
<br/>

The "basic" version of this fix would be to move both objects (aka change the position directly) in opposite directions along the collision normal by the penetration depth amount.

<p align="center">
<img src="https://github.coventry.ac.uk/217CR-2021/Teaching-Material/blob/master/Session%209/Readme%20Pictures/solving%20sphere%20pen.png">
</p> 

Because the collision resolution happens in a frame, objects with a small amount of penetration depth will be un-noticable by players when they are moved. Bigger objects with more depth will be more obvious (because they will move a larger amount), but will resolve and collide off each other without objects getting stuck or oscillating.

## Rigid Bodies

As discussed in the lecture, rigid bodies are affected by rotational motion as well as linear motion so the collision resolution phase needs to take this into account when working out the impulse.

The updated impulse formula is present in the slides.

We will now need to:
* Work out the impulse with the updated impulse formula (which takes into account rotational motion).
* Apply this impulse to linear motion (via linear velocity) as before.
* Apply this impulse to rotation motion (via rotational velocity) as before.

## Homework
* Add different co-efficients of restitution to objects in your scene so that objects bounce or stick after a collision differently depending on the object collided with. (Remember to keep within the right bounds for the co-efficient!)
* Add interpenetration resolution before collision resolution. Better penetration resolution should take into account mass when moving objects apart. (HINTS: Eberly uses this method in his _Game Physics_ book. Ian Millington also covers this in _Chapter 7 - Hard Constraints_ in his _Game Physics Engine Development_ book.)
