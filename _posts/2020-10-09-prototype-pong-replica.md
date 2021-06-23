---
layout: post
title: Prototype 12 - Pong Replica
tags: portfolio
author:
- Silver-Hornet
meta: ""
---

![Pong Replica]({{site.url}}/pong-replica.gif)

Pong Replica is a game prototype I built while following along with Noob Tuts’ [Unity 2D Pong Tutorial](https://noobtuts.com/unity/2d-pong-game) on [Noob Tuts](https://www.noobtuts.com/). The core gameplay loop is:

- Hit ball with paddle
- Don’t let ball pass your paddle
- Get the ball past your opponent

Play the game in your browser [here](https://play.unity.com/mg/other/tetris-replica-from-noob-tuts-unity-2d-pong-game-tutorial).

View my GitHub repo [here](https://github.com/silver-hornet/noobtuts-pong-replica).

## Features
- Single-screen level
- Ball movement
- Player 1 paddle
- Player 2 paddle

## What did I like about building this prototype?
Would there have been a video game industry without [Pong](https://en.wikipedia.org/wiki/Pong)? Released in 1972, it was the first game developed by Atari, and also the first commercially successful video game. Even though they were later  [sued for patent infringement by Magnavox](https://thinksetmag.com/insights/digital-detective-pong) (who created a less successful electronic table tennis game with the highly imaginative name of “Table Tennis” in 1971), Pong will forever have an important place in video game history.

So it was pretty cool to build a basic prototype of this classic game. Two paddles, one ball, and only 50+ lines of code across 2 scripts.

It’s also my first two player co-op prototype. Player 1 can move their paddle up and down using “w” and “s” keys, while Player 2 can use the up and down cursor keys.

I liked the way two player co-op was handled here. Instead of adding unnecessary code, or messing around with using `Input.GetKeyDown` and hard coding in key codes, this prototype instead uses `GetAxisRaw`, and then serializes a string reference called “axis”. Then, in the Inspector, we simply type in the name of the axis. Eg. “Vertical” for Player 1, and “Vertical2” for Player 2. Then in Unity’s Input Manager, “Vertical” already exists, so we just create a copy of that, call it “Vertical2”, and assign keys there. I feel it’s a simple, elegant solution. 

Using the Input Manager with `GetAxisRaw` also means it will work with joysticks, gamepads, etc.

## What could have been improved?
The hierarchy inside this prototype could have been cleaned up. Usually, I like to have separate folders for Materials, Prefabs, Scripts, Sprites, etc. It’s just more organized and easier to work with, although not really an issue in a tiny prototype like this.

The prototype is also missing a number of other features of the original Pong game. There’s no score keeping here. The rally never ends (the ball just bounces off all the walls). There are no menus, no sounds, and no AI.

We could create a separate ScoreManager class, then add a script on both the left and right walls to call the ScoreManager to pass on a value of 1 when there is a collision with the ball. When the ball collides with the left wall, Player 2 would get a score. And vice versa.

When the ball collides with the left or right walls, we could either make the scene reset using `SceneManager` (but ScoreManager would have to be a singleton and we’d need to pass the score value to it right before the scene resets, or we could write the values to `PlayerPrefs` to make the score persist), or we could make the ball disappear and then re-spawn it in the middle of the screen (in which case it would not be necessary to reset the scene).

Which brings me to another feature that could be improved. When the game starts, the ball always travels straight and to the right. To randomize whether it goes left or right, we could add the following to Ball.cs:

	[SerializeField] float speed = 30;
    [SerializeField] int sign;

    void Start()
    {
        sign = (int)Mathf.Sign(Random.Range(-1f, 1f));
        GetComponent<Rigidbody2D>().velocity = Vector2.right * speed * sign;
    }

If we also wanted to randomize where on the y-axis the ball spawns from, we could add two float variables (with a minimum and maximum value that matches the height of the play area), then set the ball’s `transform.position.y` to randomize between the two float values using `Random.Range()` when the ball needs to re-spawn after a rally ends.

We could also use a similar approach to randomize the angle the ball travels when it spawns (since a straight line is a bit boring and predictable).

On the performance side, there are a few things that could have been improved. I noticed that MoveRacket.cs is calling `GetComponent` every frame to apply `velocity` to paddle movement. While this isn’t an issue in a small prototype, in a bigger project this could cause performance issues. Calling `GetComponent` every frame is [costly](https://www.monkeykidgc.com/2021/02/unity-getcomponent.html). It would be better to create a variable called `myRigidbody`, cache `GetComponent<Rigidbody2D>().velocity` in `void Start()`, then use `myRigidbody.velocity` in `FixedUpdate()` instead.

For racket impact, Ball.cs is relying on using `collision.gameObject.name`. I’m not a fan of creating references to game object names for a couple of reasons. Firstly, if I accidentally (or intentionally) change the name of the relevant game object within Unity, it’s easy to forget that there might be a script somewhere in the game that is referencing the name I just changed. So that can break functionality, and I won’t easily know why.

Secondly, using `Object.name` does not provide any error messages in the debug log. So it might not be working and I’ll never know. One of the things I like about using tags and `compareTag` instead is that if a game object is missing the tag the script is trying to call, the debug log immediately lets me know what the problem is, and on which line of which script.

## Unity and C# Documentation
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)
- [BoxCollider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/BoxCollider2D.html)
- [Collider2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collider2D.html)
- [Collision](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision.html)
- [Collision.collider](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision-collider.html)
- [Collision.gameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Collision-gameObject.html)
- [CompareTag](https://docs.unity3d.com/ScriptReference/GameObject.CompareTag.html)
- [Fields](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)
- [FixedUpdate](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.FixedUpdate.html)
- [Fixing Performance Problems](https://learn.unity.com/tutorial/fixing-performance-problems-2019-3?uv=2019.3#5e85b706edbc2a0020b5e02a)
- [GameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.html)
- [GameObject.GetComponent](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.GetComponent.html)
- [Garbage Collection](https://docs.unity3d.com/2018.4/Documentation/Manual/UnderstandingAutomaticMemoryManagement.html)
- [Input.GetAxisRaw](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetAxisRaw.html)
- [Input.GetKeyDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetKeyDown.html)
- [Inspector](https://docs.unity3d.com/2018.4/Documentation/Manual/UsingTheInspector.html)
- [OnCollisionEnter2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.OnCollisionEnter2D.html)
- [PlayerPrefs](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/PlayerPrefs.html)
- [Random.Range](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Random.Range.html)
- [Return statement](https://en.wikipedia.org/wiki/Return_statement)
- [Rigidbody2D](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Rigidbody2D.html)
- [SerializeField](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SerializeField.html)
- [Singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern)
- [Start](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.Start.html)
- [Transform](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform.html)
- [Transform.position](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Transform-position.html)
- [Vector2](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Vector2.html)
- [Vector2.Normalize](https://docs.unity3d.com/ScriptReference/Vector2.Normalize.html)