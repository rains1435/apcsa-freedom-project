# Tool Learning Log

## Tool: **Phaser**

## Project: **2D Volleyball**

---

### 10/25/26:
* Since I am doing a 2D volleyball game, there are two components (the X and Y axis). Those 2 are very broad variables that is used in many fields such as mathematics. But in this house, it means speed and direction (velocity, gravity, bounce)
* **We are learning BASICS OF ARCADE PHYSICS** (since I'm using Phaser)
* Sources: 
  * [Gravity](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components-gravity) | [Velocity](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components-velocity) | [Bounce](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components-bounce) | [Body](https://docs.phaser.io/api-documentation/class/physics-arcade-body) | [Arcade Physics](https://docs.phaser.io/phaser/concepts/physics/arcade)

* **Direction/Axis**: Upward/leftward = -Y and -X | Downward/Rightward = +Y and +X
* **Velocity** is measured in pixels per second. For vertical velocity, positive means moving down and negative means moving up. So `setVelocityY(-500)` shoots upward.
* **Gravity** has two layers and they add up: The world's gravity and each body has their own. Total gravity is the sum of world and body. A negative body gravity pushes the body up against gravity.
* **Jumping** is only one BURST of a negative `setVelocityY` and world's gravity will drag the player back down. `onFloor()` will control jumping to only WHEN THE PLAYER is on another body or the world's floor (prevents jumping infinitely).
* **Bounce** is how much speed survives when body collides with something (value of 1 is 100% survival and 0 is 0% which means no rebound)
* Let's get into some tinkering! Everything will be explained in the code segment below with comments!

``` js
// game config
physics: { default: 'arcade', arcade: { gravity: { y: 900 }, debug: true } }

// in create()

// ball physics
ball = this.add.rectangle(400, 100, 30, 30, 0xffffff);
this.physics.add.existing(ball);
ball.body.setBounce(0.7).setCollideWorldBounds(true); // has bounce b/c it's a volleyball
ball.body.setGravityY(-600); // gravity of ball (is needed b/c ball needs to be floaty and slower to the ground; makes the game less frustrating): world gravity + ball gravity = 900 - 600 = 300 (300 gravity downwards while the player has gravity full effect of 900; faster falling for player)

// the player physics (notice how there isn't a setGravityY for player? yeah that explains the comment prior to this)
player = this.add.rectangle(200, 500, 40, 40, 0xff4444);
this.physics.add.existing(player);
player.body.setCollideWorldBounds(true);

// in update()
if (player.body.onFloor()) {
  player.body.setVelocityY(-500); // in the video, the player (red square) is constantly hopping after touching the ground each time... this is a good sign because it shows that when the player is on the floor, it will give the player a BURST velocity of -500 in the y-axis which means it's going to jump and jump and jump for eternity each time it's ON THE FLOOR.
}
```

**RESULT:**

https://github.com/user-attachments/assets/f1c6d953-1fd2-4e1b-b9a3-11f3b99efa58

### X/X/XX:
* Text


<!-- 
* Links you used today (websites, videos, etc)
* Things you tried, progress you made, etc
* Challenges, a-ha moments, etc
* Questions you still have
* What you're going to try next
-->
