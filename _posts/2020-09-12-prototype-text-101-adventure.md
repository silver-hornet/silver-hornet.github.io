---
layout: post
title: Prototype 5 - Text 101 Adventure
tags: portfolio
author:
- Silver-Hornet
meta: ""
---

![Text 101 Adventure]({{site.url}}/text-101-adventure.gif)

Text 101 Adventure is a game prototype I built while following along with GameDevTV’s [Complete C# Unity Game Developer 2D](https://www.udemy.com/course/unitycourse/) course on Udemy. The core gameplay loop is:

- Read text
- Make a choice

Play the game in your browser [here](https://play.unity.com/mg/other/gamedevtv-s-text101-adventure-game).

View my GitHub repo [here](https://github.com/silver-hornet/gamedevtv-text101).

## Features
- Text input
- Rooms

## What did I like about building this prototype?
As a kid, I remember getting my hands on a [Choose Your Own Adventure](https://en.wikipedia.org/wiki/Choose_Your_Own_Adventure)-type book. It was cool to choose the direction of the narrative. This prototype replicates that kind of experience.

This is a very lean prototype with no actual story. It only contains 60 lines of code. Even if the prototype contained more rooms, and more choices per room, it would still only contain 60 lines of code.

How is that possible?

It’s all thanks to ScriptableObjects. This was the first time I have used them. ScriptableObjects are a special class of their own, and are useful for storing data such as the rooms in this game. From Unity’s documentation:

> A ScriptableObject is a data container that you can use to save large amounts of data, independent of class instances. One of the main use cases for ScriptableObjects is to reduce your Project’s memory usage by avoiding copies of values. This is useful if your Project has a Prefab that stores unchanging data in attached MonoBehaviour scripts.

> Every time you instantiate that Prefab, it will get its own copy of that data. Instead of using the method, and storing duplicated data, you can use a ScriptableObject to store the data and then access it by reference from all of the Prefabs. This means that there is one copy of the data in memory.

> Just like MonoBehaviours, ScriptableObjects derive from the base Unity object but, unlike MonoBehaviours, you can not attach a ScriptableObject to a GameObject. Instead, you need to save them as Assets in your Project. 

There is only one type of `ScriptableObject` in this prototype (State.cs). It contains a text (string) field (which stores the room description) and an array of choices to proceed. Each choice loads another room, which is its own asset deriving from the State.cs ScriptableObject. 

ScriptableObjects make it easy for someone to create new rooms, with new text descriptions and choices, without having to touch the underlying code (since the code doesn’t change, no matter which room you’re in).

I like the simple way the prototype checks for keyboard input during the Update loop:

        var nextStates = state.GetNextStates();
        for (int index = 0; index < nextStates.Length; index++)
        {
            if (Input.GetKeyDown(KeyCode.Alpha1 + index))
            {
                state = nextStates[index];
            }
        }

Every frame, this for loop cycles through from 0 to the number corresponding with the length of `nextStates` array (each room has a serialized array containing all the different rooms the player can visit from there). For example, if the player hits 1 on their keyboard, that means they want to select choice 1 in the room they’re in. Since all arrays start from 0, the room corresponding with choice 1 will actually have an index of 0. So at some point during the for loop, when the index cycles back to 0, the player’s input of 1 will be evaluated as true in the if statement.

Why? Because the `KeyCode` function will look for `Alpha1 + index`, which in this case will equal the number of the key the player pressed (1). Once the if statement is true, it will run the line of code loading the room that is positioned at 0 in the index. If the player actually hit 2 on their keyboard, this would load the room at position 1 in the array (since an array starts at 0, position 1 is the second position, which corresponds to the player’s choice of 2).

## What could have been improved?
When creating a Canvas in Unity, an EventSystem is automatically created. An EventSystem is a separate game object  designed to handle input associated with interactive UI (eg. buttons). In this prototype, we’re not using that type of functionality, so the EventSystem can be deleted with no impact to the game.

This prototype doesn’t contain any sounds or music. While this is meant to be a text adventure, adding some background music and/or voice-over narration could help to set the mood.

For basic background music (that isn’t meant to change from room-to-room), we could just create a separate game object, add an Audio Source component to it, then drop in a background music clip and toggle Loop.

To add voice-over narration, we could add the following line to the State.cs ScriptableObject:

	public AudioClip voiceNarration;

Then in the Inspector, for every room, we could just drop in a voice clip in the exposed Voice Narration field.

Then, to enable it to play, we could add the following lines of code to the top of AdventureGame.cs (the same script that contains the previously discussed for loop):

	[SerializeField] AudioSource myAudioSource;
    [SerializeField] AudioClip myAudioClip;

	void Start()
    	{
        myAudioClip = state.voiceNarration;
        myAudioSource.PlayOneShot(myAudioClip);
    	}
	// The two lines above ensure that the start of the game loads its voice narration clip as well.

And then we would adjust the for loop to include the following:

	for (int index = 0; index < nextStates.Length; index++)
        {
            if (Input.GetKeyDown(KeyCode.Alpha1 + index))
            {
                myAudioSource.Stop(); // To ensure that voice narration clips don’t play over the top of clips from other rooms
                state = nextStates[index];
                myAudioClip = state.voiceNarration;
                myAudioSource.PlayOneShot(myAudioClip);
            }
        }

## Unity and C# Documentation
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)
- [Array](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Array.html)
- [Audio Clip](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioClip.html)
- [Audio Source](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioSource.html)
- [Canvas](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UICanvas.html)
- [Canvas Components](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/comp-CanvasComponents.html)
- [CreateAssetMenuAttribute](https://docs.unity3d.com/ScriptReference/CreateAssetMenuAttribute.html)
- [Event System](https://docs.unity3d.com/2018.4/Documentation/Manual/EventSystem.html)
- [Fields](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)
- [Learn conditional logic with branch and loop statements](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/branches-and-loops-local)
- [GameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.html)
- [If-else statements](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/if-else)
- [Input.GetButtonDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetButtonDown.html)
- [Input.GetKeyDown](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Input.GetKeyDown.html)
- [Inspector](https://docs.unity3d.com/2018.4/Documentation/Manual/UsingTheInspector.html)
- [KeyCode](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/KeyCode.html)
-  [MonoBehaviour](https://docs.unity3d.com/Manual/class-MonoBehaviour.html)
- [Return statement](https://en.wikipedia.org/wiki/Return_statement)
- [ScriptableObject](https://docs.unity3d.com/2018.4/Documentation/Manual/class-ScriptableObject.html)
- [SerializeField](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SerializeField.html)
- [Start](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.Start.html)
- [TextAreaAttribute](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/TextAreaAttribute.html)
- [TextMeshPro](https://docs.unity3d.com/2018.4/Documentation/Manual/com.unity.textmeshpro.html)
- [UI.Text](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/UI.Text.html)
- [Update](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Experimental.PlayerLoop.Update.html)