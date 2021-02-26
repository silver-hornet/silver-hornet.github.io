---
layout: post
title: Prototype 2 - Bubble Struggle Replica
tags: portfolio
author:
- Silver-Hornet
meta: ""
---

![Bubble Struggle Replica]({{site.url}}/bubble-struggle-replica.gif)

Bubble Struggle Replica is a game prototype I built while following along with Brackey’s [How to make a Bubble Struggle replica in Unity](https://www.youtube.com/watch?v=4jGVesn7O4g&list=PLPV2KyIb3jR5RwVEjFCiN5BvK3Quqgv_M&index=5&t=0s) tutorial on YouTube. The core gameplay loop is:

- Move left and right
- Avoid the balls
- Shoot all the balls

Play the game in your browser [here](https://play.unity.com/mg/other/bubble-struggle-replica-from-brackey-s-how-to-make-a-bubble-struggle-replica-in-unity-livestream-tutorial).

View my GitHub repo [here](https://github.com/silver-hornet/brackeys-bubble-struggle-replica).

## Features
- Single-screen level
- Player character
- Player death
- Player shoots chain
- Chain continues upward trajectory until it hits something
- Bouncing enemy ball
- Enemy ball splits in two when hit with the chain
- Enemy ball splits five times
- Game resets on player death

## What did I like about building this prototype?
One of my favourite games growing up was [Pang](https://en.wikipedia.org/wiki/Buster_Bros). My brother and I used to play it often on his Amiga 500. The gameplay loop was very satisfying, and the music and artwork was charming and memorable. So it was a joy to prototype something inspired by that.

Implementing ball splitting in this prototype was interesting. It involved instantiating two smaller versions of the ball one vector to the left and one vector to the right as soon as the player’s chain collides with the initial ball, and then immediately adding a `ForceMode2D.Impulse` force to the two new balls.

The deployment of the chain from the player was quite clever, yet simple. Using a basic square block sprite, the code extends the local scale (size) of the square block upward at a set speed. It continues to extend upwards until it collides with a ball or the ceiling.

Player movement uses `Rigidbody.MovePosition`, which transitions from an initial position to the next position, dictated by a movement speed combined with the input from a controller/keyboard. It results in smooth and consistent movement.

Also as part of player movement, I liked using `Input.GetAxisRaw` (instead of `Input.GetAxis`). GetAxisRaw feels much snappier than GetAxis, meaning that the player can instantly stop once a button/key is no longer pressed.

## What could have been improved?
Using `Time.fixedDeltaTime` in `FixedUpdate` seems redundant. According to Unity’s own documentation:

> For reading the delta time it is recommended to use `Time.deltaTime` instead because it automatically returns the right delta time if you are inside a `FixedUpdate` function or `Update` function.

Using `Collision.collider.tag` also does not seem optimal. `CompareTag` would be a better choice, since it includes validation of whether the tag exists, and is also more efficient from a [performance](https://learn.unity.com/tutorial/fixing-performance-problems-2019-3?uv=2019.3#5e85b706edbc2a0020b5e02a) perspective: 

> Another unexpected cause of heap allocations can be found in the functions `GameObject.name` or `GameObject.tag`. Both of these are accessors that return new strings, which means that calling these functions will generate garbage. Caching the value may be useful, but in this case there is a related Unity function that we can use instead. To check a GameObject’s tag against a value without generating garbage, we can use `GameObject.CompareTag()`.

There are fields (variables) in this prototype that are public that shouldn’t be. The danger in having public fields is that any other script could potentially modify these values. While this isn’t much of a concern in a small prototype like this, it could make debugging a nightmare for a much bigger project. 

There are two ways of dealing with this, depending on the reason why the field was made public to begin with. If it was made public so that it’s visible and editable in the Inspector, then it’s better to use `SerializeField`, which does not allow other scripts to access or edit the value.

But if the field was made public because other scripts need to access that field, it’s preferable to do that through its own method or properties (eg. a getter, a setter, or both) instead.

Using `Instantiate` for the ball splitting mechanic is also not optimal. Like most improvements mentioned above, this isn’t really a problem for such a small prototype, but could present performance issues for a bigger game that instantiates more objects simulatenously, and more frequently. In that situation, the CPU would need to allocate considerably more resources to manage the constant cycle of creating and removing these objects (the latter which uses Unity’s Garbage Collection). 
For those sorts of scenarios, it would be better to use a design pattern, such as an object pool. An object pool pre-instantiates all the objects a scene will need into a pool before gameplay begins. The game then reuses the objects in this pool by activating and deactivating them, which is much less costly in terms of CPU processing power.

## Code Recipes
Whenever I build anything, I look for opportunities to create reusable code that I can repurpose elsewhere. This helps me with the learning process, as well as being helpful to me and others on future prototypes and projects.

I create “gists” in GitHub to store these reusable code recipes. Here are the gists I created after building this prototype:

- [PlayerMovement](https://gist.github.com/silver-hornet/9ca8a2ca1e30a232b6d8a196a5cad24b)
- [Bouncing Ball](https://gist.github.com/silver-hornet/682c192225247dcd9c71c41cef5b8568)

## Unity and C# Documentation
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)
- [AddForce](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.AddForce.html)
- [Bool](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/bool)
- [BoxCollider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/BoxCollider2D.html)
- [Collider](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider.html)
- [Collision](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision.html)
- [Collision.collider](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision-collider.html)
- [CompareTag](https://docs.unity3d.com/ScriptReference/GameObject.CompareTag.html)
- [Destroy](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.Destroy.html)
- [Fields](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)
- [FixedUpdate](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.FixedUpdate.html)
- [Fixing Performance Problems](https://learn.unity.com/tutorial/fixing-performance-problems-2019-3?uv=2019.3#5e85b706edbc2a0020b5e02a)
- [ForceMode2D.Impulse](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/ForceMode2D.Impulse.html)
- [GameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.html)
- [GetComponent](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Component.GetComponent.html)
- [If-else statements](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/if-else)
- [Input.GetAxis](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetAxis.html)
- [Input.GetAxisRaw](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetAxisRaw.html)
- [Input.GetButtonDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetButtonDown.html)
- [Inspector](https://docs.unity3d.com/2018.4/Documentation/Manual/UsingTheInspector.html)
- [Instantiate](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.Instantiate.html)
- [Layer-based collision detection](https://docs.unity3d.com/2018.4/Documentation/Manual/LayerBasedCollision.html)
- [Null](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/null)
- [OnCollisionEnter2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider2D.OnCollisionEnter2D.html)
- [OnTriggerEnter2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider2D.OnTriggerEnter2D.html)
- [Physic Material](https://docs.unity3d.com/2018.4/Documentation/Manual/class-PhysicMaterial.html)
- [Prefabs](https://docs.unity3d.com/2018.4/Documentation/Manual/Prefabs.html)
- [Quaternion.identity](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Quaternion-identity.html)
- [Rigidbody2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.html)
- [Rigidbody2D.MovePosition](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.MovePosition.html)
- [SceneManager](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SceneManagement.SceneManager.html)
- [SerializeField](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SerializeField.html)
- [Start](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.Start.html)
- [Static](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/static)
- [Time.deltaTime](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Time-deltaTime.html)
- [Time.fixedDeltaTime](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Time-fixedDeltaTime.html)
- [Transform](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform.html)
- [Transform.localScale](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform-localScale.html)
- [Transform.position](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform-position.html)
- [Update](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.Update.html)
- [Vector2](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Vector2.html)