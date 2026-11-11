# Entry 1: WHAT AND HOW?
##### 11/9/26

## What am I making exactly???

I am making ANOTHER GAME and this time it will be a very sporty type of game... VOLLEYBALL!!! Not the conventional volleyball because instead of 6v6 or 3v3 or 2v2, it will be 1v1. Now you might be asking me how will that work? Well let me explain with what I have in mind...

### GAME: 2D VOLLEYBALL 1V1
- Player 1 and Player 2 will be separated, one player on each side of the court.
- Player (1) on the left of the court will be serving first, serving will be a toss, not a hit (if there is more time, I will implement hitting serves)
- Player (2) on the right of the court will be receiving the ball (**first touch**), setting the ball (**second touch**), then jumping to hit the ball (**third touch**) to which the Player (1) will receive/dig the ball and do the same thing player 2 did (**it's a cycle until one player doesn't get the ball; ball touches the floor of one of the sides**)
    - **ONLY 3 TOUCHES PER PLAYER; RESETS AFTER GOING OVER NET; A FOURTH TOUCH WILL BE FOUL -> POINT TO OTHER TEAM**
    - **BUMPING/SETTING** ARE THE SAME THING FOR THIS GAME BECAUSE THE CHARACTERS ARE MOST LIKELY BE CIRCLES
        - Both of these actions are held down/charging power mechanic; longer hold time = more powerful bump; it is recommended to have a short hold time if you want to self pass it to yourself to which you can hit the ball over
    - **Jumping + Hitting**: Jumping is single press (fixed height) and Hitting is held down and also depends on the angle
    - **Diving** is single press (fixed dash distance); will only bump the ball up vertical, no horizontal component.
- Since the game is 2D, there are only 1 "OUT" area per side which is like at the left end or right end of the court (there will be a line separating in-court and out-court)

### Why Phaser AGAIN???
So at first, I chose Roblox Luau as my main tool because I thought it'd be cool to make a volleyball game on roblox. Also the Luau syntax looked very simple but before I even started tinkering, I wanted to use Phaser instead. It's not because I don't want to try something new but more that I secretly liked Phaser much more due to its simplicity, portability, and accessibility. However, I am going to learn Luau in the near future just so that I can make a Roblox game that will hopefully make me some money.

---

### Tinkering Binkering

Here's what I have done so far... [Click here for raw tinkering code](../tool/phaser-tinkering.html)
- Basics of Arcade Physics 
- Movement and Holding Down

**Basics of Arcade Physics**

In a game of volleyball, if GRAVITY didn't exist, we would be floating and also we would die of no air. So I added [gravity](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components-gravity) which is as simple as reading a book (go to your game configuration and just write: `gravity: {y:900}` like you see below). You might be wondering why gravity is positive... well this is because the arcade canvas's origin begins on the top left corner, not bottom left so as you go down, y position increases. Velocity is also going to be a big part of this game. We all have a initial velocity of some value when we jump in real life so to replicate that in game, I just went into my update section and did an `if` conditional. If the player's body is [ON THE FLOOR](https://docs.phaser.io/api-documentation/class/physics-arcade-body#onfloor) (this is to prevent double jumping which is quite literally impossible in real life), then the player will have an initial upward velocity of some value (-500 in the y axis to be exact). The code below for the jumping is auto-hopping because I haven't created keybinds for jumping and it only jumps whenever its on the floor. Also do you see how the ball has [bounce](https://docs.phaser.io/api-documentation/namespace/physics-arcade-components-bounce)? yeah that's self-explanatory. Last thing, I added a counter-acting force of gravity on the in-game world gravity for the ball because the ball needs to be floaty (makes gameplay easier).

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

**Movement and Holding Down**

I won't have to explain a lot of the things done here because I have done them last year but a quick overview is that I added [keys](https://docs.phaser.io/api-documentation/class/input-keyboard-keyboardplugin) for both players: for left-side player (WASD and left-shift) and for right-side player (arrow keys and forward slash). The WASD and Arrow keys are for movement (however, S and down arrow is for bumping/setting). Left-shift and Forward slash are keys for diving (one key per player). The problem is that these keys are defined but they aren't going to work by themselves (they don't know what we are going to use them for). So I did some `if` and `if else` conditionals (look below at horizontal movement, it explains what happens; [Source](https://docs.phaser.io/api-documentation/class/input-keyboard-key#isdown)). Important to remember that the x-axis (horizontal) still starts from the left like a graph so it stays as is. After finishing up horizontal movement, I moved onto jumping (vertical). Jumping isn't a held down like moving left and right so then instead of `this.p1.W.isDown`, we do, `Phaser.Input.Keyboard.JustDown(this.p1.W) && body.onFloor()` for the `if` condition ([Source](https://docs.phaser.io/api-documentation/namespace/input-keyboard#justdown)). `JustDown(this.p1.W)` only processes single press, not holding. Lastly, let's not forget about bumping/setting (using the 's' key) and diving. For these two actions, the results are printed via the console log (watch the video below the code to see what I mean). The reason I did console for these two is because I wanted to see if diving worked and also knowing the time increments of holding down, I can make charging power.

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
    body.setVelocityX(-250); // A held = move left (negative velocity)
} else if (this.p1.D.isDown) {
    body.setVelocityX(250); // D held = move right (positive velocity)
} else {
    body.setVelocityX(0); // otherwise, don't move (no velocity)
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

## Engineering Design Process (EDP)

I am all the way back to the start... **Step 1: Define the Problem**. I'm going to be completely honest, there isn't really a "problem" but it's more that I just want to make a game for people to have fun. I guess you can consider that a "problem" but that would be a reach in my opinion. I'm making this game more to symbolize how far I've gotten in coding and for the fun of others. I feel like if I try to invent some type of problem for this project, it would ruin the purpose of me making this game; I want to leave behind a legacy and this is something sentimental rather a solution to an issue. I hope you understand me Mr. Mueller.

## Skills

**How to Read**: I read a lot of Phaser documentations for tinkering/learning logs in the past 3 weeks. Sometimes, the code I see isn't very clearly explained to me so I have developed a skill of reading with context clues. It takes like 3 seconds to read but a few minutes to think about what this code will do. For example, I didn't really know the difference between `JustDown()` and `.isDown` until I read a few more times. One thing I am aware of is that the documentation is going to get harder to read and understand as I progress deeper so that's why I am practicing this skill.

**Consideration**: I know I've said it before that I didn't want their to a problem for my project that I wanted to solve but the reason I'm mentioning consideration is more game-related than real life related. I had to think of a way to make the game more playable for players since volleyball is a one on one sport. First, I got rid of was blocking because if the hit does go through the block, there won't be anyone behind the player to get the ball up (thus blocking is technically useless in 1v1s). Second, I made the ball more floaty (less affected by gravity) so that players can get to the ball easier in a real game. I think that's all. But of course, I will add more Quality of Life later on.

---

## Next Step

If I must create a problem to solve, then I will do part 2 which is researching the issue but if I don't need to, then I will be doing more tinkering and learning instead.


[Next](entry02.md)

[Home](../README.md)