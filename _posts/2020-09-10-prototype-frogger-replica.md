---
layout: post
title: Prototype 4 - Frogger Replica
tags: portfolio
author:
- Silver-Hornet
meta: ""
---

![Frogger Replica]({{site.url}}/frogger-replica.gif)

Frogger Replica is a game prototype I built while following along with Brackeys’ [How to make a Frogger Replica in Unity](https://www.youtube.com/watch?v=wZt6qDDx-2o) tutorial on YouTube. The core gameplay loop is:

- Move across the road
- Avoid cars
- Get to the other side

Play the game in your browser [here](https://play.unity.com/mg/other/brackeys-frogger-replica).

View my GitHub repo [here](https://github.com/silver-hornet/brackeys-frogger-replica).

## Features
- Single-screen level
- Player character
- Player death
- Game resets on player death
- Moving cars (randomized speed, travelling in two different directions)
- Car spawner (randomized spawn point)
- Lanes
- Score

## What did I like about building this prototype?
I remember first playing a port of [Frogger](https://en.wikipedia.org/wiki/Frogger) on my brother’s Amiga 500 when I was a kid. It was a fun, although frustrating, game. But there was a lot of satisfaction when I made the frog reach the top of the screen without being run over by a truck, or drowned because of those damn, unreliable turtles. For a game that was originally released in arcades in 1981, it’s amazing how well the core gameplay has withstood the test of time. And that’s only further proven with the recent success of [Crossy Road (2014)](https://en.wikipedia.org/wiki/Crossy_Road), an endless runner inspired by Frogger.

Even though this prototype is a heavily stripped back version of Frogger, the core gameplay is here, and it feels satisfying to play. Get to the top of the screen to get 100 points. The scene then resets, keeping your score, until you eventually get run over and the score resets.

With no bells and whistles or menu screens, this entire prototype contains just over 100 lines of code.

Although this was a very short and simple prototype, access modifiers are used correctly here. With one accidental exception, there are no fields that are public that shouldn’t be. Instead, for any field that needs to be exposed in Unity’s Inspector (but doesn’t need to be exposed to other classes), the prototype uses the `[SerializeField]` access modifier. A lot of courses neglect doing this, and instead make all these types of fields public. So it’s good to see this being done properly in this prototype. Having said that, these things aren’t really a major issue in such small projects. But I still like to develop correct habits.

I like the simple approach to spawning cars. There is an invisible Car Spawner game object containing an array of transforms (positions in the game environment). Each transform is located at the end of a lane. The Car Spawner class then randomly selects one of the transforms every 0.3 seconds and instantiates a new car.

I like that different lanes have cars going in different directions. The direction was accomplished in a very simple way. Instead of making the code more complicated to determine whether a car should drive left or right, all the cars are automatically set to drive right. That is done using `transform.right`, which basically moves the car by 1 positive vector every frame. But it considers “right” to be to the object’s right (considering its rotation in local space), rather than what is “right” in world space. So by flipping the rotation of any of our spawn points in the Inspector, we can make a car in a particular lane travel left, instead of right.

While player movement (input) is often done through `GetAxis` or `GetAxisRaw`, here we are using `GetKeyDown` instead. Usually this would not be recommended for input, but because we are not looking for continuous movement (we only want to make the frog move one vector each time the relevant key is pressed), it makes sense. It may become a problem if the prototype is ported to another platform, such as console, though. In that case, it might be better to experiment with `GetButtonDown` instead, although this wasn’t designed for movement either.

## What could have been improved?
You might quickly realize that you can move the frog beyond the left, right, and bottom screen boundaries. This, obviously, would need to be corrected. An easy way of doing that would be to use the GitHub gist code recipe I’ve previously created: [Set Screen Boundaries for 2D Player](https://gist.github.com/silver-hornet/268f5053db548af6dae51b531a0bcf79). Basically, it would involve clamping how far the player can move on the x- and y-axis.

The car sprite is a bit sloppy. I used a PSD that should have imported correctly into Unity, but lost its transparent layer during import, which means the car sprite has a white background. This can easily be fixed by exporting the PSD as a PNG instead.

The frog sprite is also very simple. It could have been improved by having several versions of the frog in various movement states, then using the Animator and Animation windows in Unity to build some animation states and transitions that trigger when the frog starts moving.

This prototype doesn’t contain any sounds or music. It’s amazing how much of a difference these things can make to the player experience. Audio could have been implemented by using an `AudioManager` and `AudioClip` functions, then implementing a singleton pattern to have music playing seamlessly in the background (even when the scene resets after player death). Adding some simple sound effects to key things like frog movement, frog death (perhaps a splat sound), as well as car engine sounds would have been great. With the latter, we could  get a bit fancier, by adding an Audio Listener to the frog (and removing it from the Main Camera default), and then using a 3D spatial blend in Audio Source on the car engine sound. This would then make the car sound closer when it’s closer to the frog, thereby increasing immersion and tension.

There are no managers of any sort in this prototype. Usually, a game will have a few different types of manager classes, such as GameManager (which would track lives, scores, scene management, etc), AudioManager, etc. However, the simplicity of this prototype doesn’t require it.

Using `Collision.collider.tag` also does not seem optimal. `CompareTag` would be a better choice, since it includes validation of whether the tag exists, and is also more efficient from a [performance](https://learn.unity.com/tutorial/fixing-performance-problems-2019-3?uv=2019.3#5e85b706edbc2a0020b5e02a) perspective: 

> Another unexpected cause of heap allocations can be found in the functions `GameObject.name` or `GameObject.tag`. Both of these are accessors that return new strings, which means that calling these functions will generate garbage. Caching the value may be useful, but in this case there is a related Unity function that we can use instead. To check a GameObject’s tag against a value without generating garbage, we can use `GameObject.CompareTag()`.

This prototype also forgot to handle all the spawned cars once they have left the screen. Currently, the cars just continue to drive forever. So if the prototype were to be running for a long time, performance might be affected due to cars being constantly spawned and never destroyed. This could be fixed by having each spawned car check if it has reached the boundary of the screen, then calling the `Destroy` function on itself.

But even better, we could use an object pool design pattern to activate and deactivate these car game objects. That would be more performant, although, like most improvements mentioned above, this isn’t really a problem for such a small prototype. But it could present performance issues for a bigger game that instantiates and destroys more objects more frequently. In that situation, the CPU would need to allocate considerably more resources to manage the constant cycle of creating and removing these objects (the latter which uses Unity’s Garbage Collection). Using an object pool would prevent those sorts of performance issues, by pre-instantiating all the objects a scene will need into a pool before gameplay begins. The game then reuses the objects in this pool by activating and deactivating them, which is much less costly in terms of CPU processing power.

## Code Recipes
Whenever I build anything, I look for opportunities to create reusable code that I can repurpose elsewhere. This helps me with the learning process, as well as being helpful to me and others on future prototypes and projects.

I create “gists” in GitHub to store these reusable code recipes. Here are the gists I created after building this prototype:

- [2D Top-Down Character Movement](https://gist.github.com/silver-hornet/a65640d97a1c2de3691f53ed8afd2d2d)
- [2D Moving Object](https://gist.github.com/silver-hornet/6b1d8d570bea0958b6a6f1aa9c38eeed)
- [Object Spawner](https://gist.github.com/silver-hornet/bc5d651295d92cb251c6f97ca03fc9eb)

## Unity and C# Documentation
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)
- [Animation](https://docs.unity3d.com/2018.4/Documentation/Manual/AnimationSection.html)
- [Array](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Array.html)
- [Audio Clip](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioClip.html)
- [Audio Listener](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioListener.html)
- [Audio Source](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioSource.html)
- [BoxCollider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/BoxCollider2D.html)
- [Canvas](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UICanvas.html)
- [Canvas Components](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/comp-CanvasComponents.html)
- [CircleCollider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/CircleCollider2D.html)
- [Collider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider2D.html)
- [Collision](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision.html)
- [Collision.collider](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision-collider.html)
- [CompareTag](https://docs.unity3d.com/ScriptReference/GameObject.CompareTag.html)
- [Destroy](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.Destroy.html)
- [Debug.Log](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Debug.Log.html)
- [Fields](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)
- [FixedUpdate](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.FixedUpdate.html)
- [Fixing Performance Problems](https://learn.unity.com/tutorial/fixing-performance-problems-2019-3?uv=2019.3#5e85b706edbc2a0020b5e02a)
- [GameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.html)
- [Garbage Collection](https://docs.unity3d.com/2018.4/Documentation/Manual/UnderstandingAutomaticMemoryManagement.html)
- [If-else statements](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/if-else)
- [Input.GetAxis](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetAxis.html)
- [Input.GetAxisRaw](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetAxisRaw.html)
- [Input.GetButtonDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetButtonDown.html)
- [Input.GetKeyDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetKeyDown.html)
- [Inspector](https://docs.unity3d.com/2018.4/Documentation/Manual/UsingTheInspector.html)
- [Instantiate](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.Instantiate.html)
- [KeyCode](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/KeyCode.html)
- [Object.ToString](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.ToString.html)
- [OnTriggerEnter2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider2D.OnTriggerEnter2D.html)
- [Prefabs](https://docs.unity3d.com/2018.4/Documentation/Manual/Prefabs.html)
- [Random.Range](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Random.Range.html)
- [Rigidbody2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.html)
- [Rigidbody2D.MovePosition](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.MovePosition.html)
- [SceneManager](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SceneManagement.SceneManager.html)
- [SerializeField](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SerializeField.html)
- [Singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern)
- [Start](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.Start.html)
- [Static variables in Unity](https://gamedevbeginner.com/how-to-get-a-variable-from-another-script-in-unity-the-right-way/#statics)
- [Time.fixedDeltaTime](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Time-fixedDeltaTime.html)
- [Time.time](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Time-time.html)
- [Transform](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform.html)
- [Transform.position](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform-position.html)
- [UI.Text](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/UI.Text.html)
- [Update](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.Update.html)
- [Vector2](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Vector2.html)