---
layout: post
title: Prototype 6 - Number Wizard
tags: portfolio
author:
- Silver-Hornet
meta: ""
---

![Number Wizard]({{site.url}}/number-wizard.gif)

Number Wizard is a game prototype I built while following along with GameDevTV’s [Complete C# Unity Game Developer 2D](https://www.udemy.com/course/unitycourse/) course on Udemy. The core gameplay loop is:

- Think of a number between 1 and 1000
- Respond to computer’s guess

Play the game in your browser [here](https://play.unity.com/mg/other/gamedevtv-s-number-wizard).

View my GitHub repo [here](https://github.com/silver-hornet/gamedevtv-number-wizard).

## Features
- Menu screen
- Game screen
- Win screen
- Guess Higher button
- Guess Lower button
- Guess Correct button
- Restart game
- Quit game

## What did I like about building this prototype?
This was another small and simple prototype, containing only 66 lines of code. There are no RigidBodies, no collisions, no if statements, no for loops, no update loops. The prototype is completely UI-based, which means nothing happens until you click one of the buttons.

This was my first prototype using UI buttons, and also my first prototype with the ability to quit out of the game using `Application.Quit()` (importantly, this only works on a Windows, Linux, or Mac build of the prototype; it isn’t designed to work on other platforms).

Although this was a very short and simple prototype, access modifiers are used correctly here. There are no fields that are public that shouldn’t be. Instead, for any field that needs to be exposed in Unity’s Inspector (but doesn’t need to be exposed to other classes), the prototype uses the `[SerializeField]` access modifier. A lot of courses neglect doing this, and instead make all these types of fields public. So it’s good to see this being done properly in this prototype. Having said that, these things aren’t really a major issue in such small projects.

I like how nicely structured and readable the code is. And its underlying logic is easy to grasp. When the game starts, the AI randomly guesses an integer between 1 and 1000. This guess is converted to a string and displayed in the UI.

If we click the Higher button, the AI sets its new minimum guess to the previous guess +1, then guesses a random number either between that and 1000 (for its first guess) or the previous maximum guess -1.

If we click the Lower button, the AI sets its new maximum guess to the previous guess -1, then guesses a random number either between that and 1 (if it’s the AI’s first guess) or the previous minimum guess +1.


## What could have been improved?
I have found a bug in the logic. Right now, if you pretend the AI never guesses your number, and you keep making it guess even when it’s narrowed down the possibilities to just one, eventually the AI’s guesses will become higher than the maximum of 1000.

This appears to be happening due to a few things.

`Random.Range` has two arguments: a minimum value, and a maximum value. If the minimum value becomes greater than the maximum value, the values are automatically swapped. This problem is happening here. The min value is constantly increasing by 1 until it becomes larger than the max value, and then other funky behaviour starts occurring.

The underlying cause of this is that the min and max variables associated with minimum and maximum guesses are able to keep changing throughout the game. This means the AI never actually knows what the starting range was after the first guess.

The AI should instead check against some other value that doesn’t change. We know the range should always be between 1 and 1000, inclusive. So the min and max values should check against that. They shouldn’t be free to change themselves to whatever they would like.

So to solve this first issue, we could serialize two new variables (gameMin and gameMax) at the top of the script, then set their values to 1 and 1000 in the Inspector. Then back in the script we can use `Mathf.Clamp` to clamp all guesses to between the gameMin and gameMax values.

    void Start()
    {
        max = gameMax;
        min = gameMin;
	}

 	public void OnPressHigher()
    {
        min = Mathf.Clamp(guess + 1, gameMin, gameMax);
        NextGuess();
    }

    public void OnPressLower()
    {
        max = Mathf.Clamp(guess - 1, gameMin, gameMax);
        NextGuess();
    }

The above still isn’t a perfect solution though. While it makes it impossible to go above 1000, it still allows some funky behaviour at the minimum end of the range.

We could probably add some more complicated logic to help fix these edge cases, but in this situation I’m thinking it might just be better to give the player feedback that they have messed up. It’s not the AI’s fault the player “forgot” their number!

So I would add a couple of if statements to the following methods in NumberWizard.css:

	public void OnPressHigher()
    {
        if (guess == gameMax)
        {
            guessText.text = “You have forgotten your number, you dill!”;
        }
        else
        {
            min = Mathf.Clamp(guess + 1, gameMin, gameMax);
            NextGuess();
        }
    }

    public void OnPressLower()
    {
        if (guess == gameMin)
        {
            guessText.text = “You have forgotten your number, you dill!”;
        }
        else
        { 
            max = Mathf.Clamp(guess - 1, gameMin, gameMax);
            NextGuess();
        }
    }

And that seems to fix the problem. Like all things coding, there are probably many ways to do the above. This was the simplest way I could think of for now.

To improve it further (and to prevent some new bugs), I could also add a reference to the UI buttons in the code above, and then disable the Higher and Lower buttons once the guess equals gameMin or gameMax. This could be done with something like `higherButton.SetActive(false)` (assuming a game object reference to higherButton exists in the script).

Lastly, a nice little addition to this prototype would be a click sound every time the player clicks any of the buttons. This could easily be accomplished by adding an AudioSource, adding an AudioClip to it, then using `AudioSource.Play()` in the if statements further above.

## Unity and C# Documentation
- [Access Modifiers](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers)
- [Application.Quit](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Application.Quit.html)
- [Audio Clip](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioClip.html)
- [Audio Source](https://docs.unity3d.com/2018.4/Documentation/Manual/class-AudioSource.html)
- [Build Settings](https://docs.unity3d.com/2018.4/Documentation/Manual/BuildSettings.html)
- [Canvas](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UICanvas.html)
- [Canvas Components](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/comp-CanvasComponents.html)
- [Fields](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)
- [GameObject](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.html)
- [GameObject.SetActive](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/GameObject.SetActive.html)
- [Inspector](https://docs.unity3d.com/2018.4/Documentation/Manual/UsingTheInspector.html)
- [Mathf.Clamp](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Mathf.Clamp.html)
- [Object.ToString](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Object.ToString.html)
- [Prefabs](https://docs.unity3d.com/2018.4/Documentation/Manual/Prefabs.html)
- [Random](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Random.html)
- [Random.Range](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/Random.Range.html)
- [Scene.buildIndex](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SceneManagement.Scene-buildIndex.html)
- [SceneManager](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SceneManagement.SceneManager.html)
- [SerializeField](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/SerializeField.html)
- [Start](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/MonoBehaviour.Start.html)
- [TextMeshPro](https://docs.unity3d.com/2018.4/Documentation/Manual/com.unity.textmeshpro.html)
- [UI.Text](https://docs.unity3d.com/2018.4/Documentation/ScriptReference/UI.Text.html)