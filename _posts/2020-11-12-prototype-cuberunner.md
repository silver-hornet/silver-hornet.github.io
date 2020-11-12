---
layout: post
title: Prototype Cuberunner
categories: prototypes
author:
- Silver-Hornet
meta: ""
---


Cuberunner is a game prototype I built while following along with Brackeys’ YouTube tutorial. The core gameplay loop is:

- Move left and right
- Stay on the track
- Avoid obstacles
- Get to the finish line

You can play the game in your browser here. And you can view my GitHub repo here.

[SCREENSHOT]

## How It Works

The game architecture consists of:
- 4 scenes (Menu, Level 1, Level 2, Credits)
- 9 types of game objects
- 9 scripts

[DIAGRAM]

### The Menu

The game loads the Menu scene first, which contains a Canvas  with welcome text and a Start button. The Canvas is a UI game object that is the parent object of other UI game objects within it (such as text and buttons). The Canvas is set to scale with screen size, which means that it will adjust to the user’s screen size, regardless of device.

The Start button is hooked up to a script called Menu.cs, which contains a StartGame() method. This method determines the BuildIndex number of the current scene (Menu), then adds 1. In this case, the next BuildIndex number refers to Level 1. So when the Start button is clicked, the button calls StartGame(), which loads up Level 2.

### Level 1

Level 1 contains 9 types of game objects:
- Main Camera
- Directional Light
- Ground
- Player
- Canvas
- EventSystem
- GameManager
- End
- Obstacles

#### Main Camera
By default, the main camera sits in a fixed position. Since our player will be moving forward, we will have to make the camera follow our player. This is why the Main Camera is hooked up to a script called CameraFollow. This is an example of good naming. The script name tells us what its purpose is.

CameraFollow.cs only has one method, Update(). And Update() does only one thing here. It updates the camera’s position so that it’s always the same as the player’s position, plus an offset. In this case, the offset is -5 on the Z-axis, which means that the camera will always be 5 world units behind our player. Without the offset, the camera would instead be positioned exactly where the player is, so you wouldn’t see the player. But this could be cool if you were after a first person perspective instead.

#### Directional Light
The Directional Light game object is a default object that is always loaded in a Scene. It represents the sun and lights up our scene. In this case, we’re not making any adjustments to it. The default settings are just fine. But adjusting its Transform (particularly the Rotation) can result in some cool lighting affects that can affect the mood of the scene.

#### The Ground
The Ground game object is a primitive – initially a cube in this case – that has been resized to fit our needs. We’ve adjusted its width and its length. Its height doesn’t matter, since our player will only be moving on the surface. We’ve added a Box Collider component, which is the exact same size of the Ground game object itself. This Box Collider ensures that any objects sitting on top of the ground (such as the player or obstacles) actually stay on the ground. Without the Box Collider, these objects would fall right through the ground.

We’ve also attached a Physic Material to the Box Collider. The Physic Material only job is to make the surface of the ground slippery, by removing any default friction and bounciness. This results in much smoother player movement.

#### The Player
The Player game object is also a primitive. In this case, a true cube. We’ve attached 5 components to it: a Box Collider, a RigidBody, and 3 scripts.

The Box Collider is the same size as the player. It ensures that the player can collide with other game objects, including the ground and any objects on its surface. Without the Box Collider, the player would just fall through the ground into a never-ending, dark void. Kind of like the year 2020...

The RigidBody applies physics to our player. This means things like giving the player Mass, Drag, Gravity, and applying forces. Without the RigidBody, [can’t make the player move, and can’t register collisions? Double check this]

The Player has 3 scripts attached. PlayerMovement.cs, PlayerCollision.cs, and Score.cs. Once again, these are good examples of script names. The names tell us exactly what each script does.

PlayerMovement.cs contains a FixedUpdate() method. FixedUpdate() [explain what it is]. In this case, this method is applying a non-stop forward force to our player. Through using two If statements, the script also checks if we’re pressing the left or right cursor keys. If we are, the script applies sideways force to match the direction of the cursor key. An If statement here also checks whether the player has fallen off the edge of the ground. If it has, then the script calls the EndGame() method in the GameManager, which invokes a Restart() method. The Restart() method checks the name of the current scene, then reloads it.

PlayerCollision.cs contains an OnCollisionEnter() method. Using an If statement, this method checks the tag of the object the player collided with. Since we applied the tag “Obstacles” to all the obstacles in the game, it checks to see if the object our player collided with had the “Obstacle” tag. If it does, we disable the player’s movement, then call the GameManager’s EndGame() method.

Score.cs contains an Update() method, which means it’s constantly updating every frame. This method checks the player’s current position on the Z-axis, which will be a float.  It then converts the float to a string, and then passes the string to the text component of the scoreText game object, which is a child object of the Canvas. This prints the score (our distance travelled) at the top of our game screen, and because it’s all happening in the Update() method, the score is dynamically updating every frame throughout our game session.

####Canvas


## Download



## Useful Links

