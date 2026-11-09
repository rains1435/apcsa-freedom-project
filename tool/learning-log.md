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

---

### 11/8/26:
* Today I am going to be doing something I've done last year using phaser but adding a new addition... I am going to show you **MOVEMENT and the addition of charging (holding down a key) vs single click (just pressed)**
* Sources:
  * [KeyboardPlugin](https://docs.phaser.io/api-documentation/class/input-keyboard-keyboardplugin) | [Key](https://docs.phaser.io/api-documentation/class/input-keyboard-key) | [Phaser.Input.Keyboard](https://docs.phaser.io/api-documentation/namespace/input-keyboard) | [Input.Keyboard.Events](https://docs.phaser.io/api-documentation/event/input-keyboard-events) | [Phaser.Input.Keyboard.KeyCodes](https://docs.phaser.io/api-documentation/namespace/input-keyboard-keycodes)

* Since this game is two-player, we will give the two players different keys they'll use
  * Player 1 will use W,A,S,D and Left Shift
  * Player 2 will use arrow keys and right slash (/)

* **Holding down** is best for moving horizontally (walking using A or D / left or right arrow) and also it is good for **CHARGING**
  * **Charging:** this will be used on the "s" key and the "down arrow key" as they will be the bumping action in the volleyball game (held long enough before ball contacts, the bump will have more power... otherwise a short hold time will just bump the ball upwards and a bit forward)

* **Single click** is best for jumping (using W or up arrow key) and diving (Shift or "/")
  * Jumping only works when the key for it is pressed and they are on the ground (**body.onFloor()**)
  * Diving, for today, will be tested on whether it works or not (a dive test via printing into console log)

**TINKERING TIME!!!**

``` js
// in create()
this.p1 = this.input.keyboard.addKeys('W,A,S,D'); // Player 1's keys
this.p1dive = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.SHIFT); // Player 1's dive key
this.p2 = this.input.keyboard.createCursorKeys(); // Player 2's arrow keys
this.p2dive = this.input.keyboard.addKey(Phaser.Input.Keyboard.KeyCodes.FORWARD_SLASH); // Player 2's dive key (/)

---

// in update()
const body = player.body; 

// player one's horizontal movement
if (this.p1.A.isDown) {
    body.setVelocityX(-250); // A held = move left
} else if (this.p1.D.isDown) {
    body.setVelocityX(250); // D held = move right
} else {
    body.setVelocityX(0); // otherwise, don't move
}
// player one's jumping movement
if (Phaser.Input.Keyboard.JustDown(this.p1.W) && body.onFloor()) {
    body.setVelocityY(-500);   // W just pressed AND player is on the ground = jump up
}
// player one's charging (TESTING in console log)
// how long the key is held down for (important to know because later on, time will be used for power charging)
if (this.p1.S.isDown) {
    console.log('charging', this.p1.S.getDuration(), 'ms'); 
} 
if (Phaser.Input.Keyboard.JustUp(this.p1.S)) {
    console.log('released after', this.p1.S.duration, 'ms');
}

// BOTH players' diving (TESTING in console log)
if (Phaser.Input.Keyboard.JustDown(this.p1dive)) {
    console.log('Player 1 dive key pressed');
} 
if (Phaser.Input.Keyboard.JustDown(this.p2dive)) {
    console.log('Player 2 dive key pressed');
}
```

**RESULT:**

https://github.com/user-attachments/assets/ee0b8b02-ee1c-4796-8e8b-c256c5b2610b

---

### XX/X/XX:

<!-- 
* Links you used today (websites, videos, etc)
* Things you tried, progress you made, etc
* Challenges, a-ha moments, etc
* Questions you still have
* What you're going to try next
-->
