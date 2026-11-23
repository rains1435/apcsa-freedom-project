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

### 11/22/26:
- In volleyball, we need the ball to be able to be interacted with and mainly with the players, net, and the floor... so we're going to learn about **how things touch!**
- Sources:
  - [Arcade Physics](https://docs.phaser.io/phaser/concepts/physics/arcade) | [Arcade Factory](https://docs.phaser.io/api-documentation/class/physics-arcade-factory) | [Arcade Components](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components) | [Math Distance](https://docs.phaser.io/api-documentation/namespace/math-distance)
- Today's lesson:
  - Collider vs Overlap
    - Collider = when two objects interact, they get pushed away from each other (can be used for ball and net interaction)
    - Overlap = when two objects interact, they dont get pushed away from each other (it's like a sensor)
  - Callback = "call me back when this or that happens" or in other words, Phaser automatically runs when two object touches and it receives information that they touched
  - Static = SOME things that never move
    - A **Static Object** (such as the net or the floor) isn't pushed around and doesn't fall like ball would. It can still bump into other things and make things bounce but it doesn't move.
    - An important thing to remember is that a static object's invisible hitbox doesn't automatically change to fit the picture of the object if the picture is resized. Thus you'll need to use `refreshBody()` -> for example: `floor.refreshBody();`
  - The net is going to be a static, thin, collidable rectangle
  - Player's hitbox for the ball
    - This will be a distance related thing. When the player releases their key for bumps and hits, the game will ask if the player is in reach of the ball.
      - Distance: the distance between the player's center and the ball's center. If the player's reach is smaller than the set distance, then it counts as a hit or bump
      - Circle scanner: place an invisible 'reach' zone around the player and ask Phaser which objects are touching the circle. Even if the ball grazes the the edge of that zone, phaser will count it as hit and include it in their list

**TINKERING TIME!!!**

``` js 
// in create()

// net
net = this.add.rectangle(400, 600, 10, 175, 0xffffff); // a thin white tall rectangle net
this.physics.add.existing(net, true); // give it physics; "true" means static (never moves)
this.physics.add.collider(ball, net); // ball bounces off the net
this.physics.add.collider(player, net); // the player can't walk through the net

// checking for ball-player overlapping
this.physics.add.overlap(ball, player, contactCheck); // creates the relationship

function contactCheck() {
    console.log("ball is touching the player"); // prints message when relationship is true
}

// in update()

// distance based checking system for whether player reaches ball or not
if (Phaser.Input.Keyboard.JustUp(this.p1.S)) {
    const dist = Phaser.Math.Distance.Between(player.x, player.y, ball.x, ball.y);

    if (dist < 80) { 
        console.log("HIT", dist); // only prints when the distance is less than 80 between the player and ball
    } else {
        console.log("miss", dist); // otherwise, 80 or more, prints miss
    }
}

// circle hitbox based checking system for whether player reaches ball or not
if (Phaser.Input.Keyboard.JustUp(this.p1.S)) {
    const nearby = this.physics.overlapCirc(player.x, player.y, 40); // the invisible circle hitbox on the player (40 is the radius of the circle)

    if (nearby.includes(ball.body)) {
        console.log("HIT (for circle check)"); // when 'S' key is released, circle will be drawn around the player to check whether the ball is in the circle's radius or even grazing the circle's edges. If yes, prints message as shown.
    } else {
        console.log("MISS (for circle check)"); // otherwise, not in the circle's radius, prints message as shown
    }
}
```

**RESULT:**

https://github.com/user-attachments/assets/4853ac6d-73f6-4ba7-916f-2d0e798b0bca

---

### X/X/XX

<!-- 
* Links you used today (websites, videos, etc)
* Things you tried, progress you made, etc
* Challenges, a-ha moments, etc
* Questions you still have
* What you're going to try next
-->
