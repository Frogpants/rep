---
layout: base
title: Course Outlines
image: /images/mario_animation.png
hide: true
---
<!-- Liquid:  statements -->
<!-- Include submenu from _includes to top of pages -->
{% include nav_home.html %}
<!--- Concatenation of site URL to frontmatter image  --->
{% assign sprite_file = site.baseurl | append: page.image %}
<!--- Has is a list variable containing mario metadata for sprite --->
{% assign hash = site.data.mario_metadata %}
<!--- Size width/height of Sprit images --->
{% assign pixels = 256 %}
<!--- HTML for page contains <p> tag named "Mario" and class properties for a "sprite"  -->
<p id="mario" class="sprite"></p>
<audio id="myAudio" src="CantTouchThis2.mp3" loop></audio>
<!--- Embedded Cascading Style Sheet (CSS) rules,
        define how HTML elements look
--->
<style>
  /*CSS style rules for the id and class of the sprite...
  */
  .sprite {
    height: {{pixels}}px;
    width: {{pixels}}px;
    background-image: url('{{sprite_file}}');
    background-repeat: no-repeat;
  }
  /*background position of sprite element
  */
  #mario {
    background-position: calc({{animations[0].col}} * {{pixels}} * -1px) calc({{animations[0].row}} * {{pixels}}* -1px);
  }
</style>
<!--- Embedded executable code--->
<script>
  const audioElement = document.getElementById('myAudio');
  function playSong() {
  if (audioElement.paused) {
    audioElement.play();
    console.log("Song is playing!");
  } else {
    audioElement.pause();
    audioElement.currentTime = 0;
    console.log("Song is paused.");
  }
}
  ////////// convert YML hash to javascript key:value objects /////////
  var mario_metadata = {}; //key, value object
  {% for key in hash %}
  var key = "{{key | first}}"  //key
  var values = {} //values object
  values["row"] = {{key.row}}
  values["col"] = {{key.col}}
  values["frames"] = {{key.frames}}
  mario_metadata[key] = values; //key with values added
  {% endfor %}
  ////////// game object for player /////////
  class Mario {
    constructor(meta_data) {
      this.tID = null;  //capture setInterval() task ID
      this.positionX = 0;  // current position of sprite in X direction
      this.currentSpeed = 0;
      this.marioElement = document.getElementById("mario"); //HTML element of sprite
      this.pixels = {{pixels}}; //pixel offset of images in the sprite, set by liquid constant
      this.interval = 100; //animation time interval
      this.obj = meta_data;
      this.marioElement.style.position = "absolute";
    }
    animate(obj, speed) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;
      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        this.marioElement.style.left = `${this.positionX}px`;
        this.positionX += speed;
        frame = (frame + 1) % obj.frames;
        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }
    animateFlipped(obj, speed,) {
      let frame = 0;
      const row = obj.row * this.pixels;
      this.currentSpeed = speed;
      this.tID = setInterval(() => {
        const col = (frame + obj.col) * this.pixels;
        this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
        // Flip the image for left movement
        this.marioElement.style.transform = 'scale(-0.2, 0.2)';
        this.marioElement.style.left = `${this.positionX}px`;
        this.positionX += speed;
        frame = (frame + 1) % obj.frames;
        const viewportWidth = window.innerWidth;
        if (this.positionX > viewportWidth - this.pixels) {
          document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
        }
      }, this.interval);
    }
    startRight() {
      this.stopAnimate();
      this.animate(this.obj["Walk"], 5);
    }
    startRunning() {
      this.stopAnimate();
      this.animateFlipped(this.obj["Run1"], 10);
    }
    startRunningL() {
      this.stopAnimate();
      this.animateFlipped(this.obj["Run2"], -10);
    }
    startLeft() {
      this.stopAnimate();
      this.animate(this.obj["Walk"], -5);
    }
    startCheering() {
      this.stopAnimate();
      this.animate(this.obj["Cheer"], 0);
    }
    startFlipping() {
      this.stopAnimate();
      this.animate(this.obj["Flip"], 0);
    }
    startResting() {
      this.stopAnimate();
      this.animate(this.obj["Rest"], 0);
    }
    stopAnimate() {
      clearInterval(this.tID);
    }
    startPuffing() {
      this.stopAnimate();
      this.animate(this.obj["Puff"], 0);
    }
  }
  const mario = new Mario(mario_metadata);
  ////////// event control /////////
  window.addEventListener("keydown", (event) => {
    if (event.key === "ArrowRight") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.startRight();
        } else if (mario.currentSpeed === 5) {
          mario.startRunning();
        }
      }
    }
    if (event.key === "ArrowLeft") {
      event.preventDefault();
      if (event.repeat) {
        mario.startCheering();
      } else {
        if (mario.currentSpeed === 0) {
          mario.startLeft();
          playSong();
        }
      }
    }
    if (event.key === "ArrowUp") {
        event.preventDefault();
        if (event.repeat) {
        mario.startFlipping();
      } else {
        mario.startFlipping()
      }
         // Call the function to set Mario to the resting state
      }
      if (event.key === "ArrowDown") {
        event.preventDefault();
        if (event.repeat) {
        mario.startPuffing();
      } else {
        mario.startPuffing()
      }
         // Call the function to set Mario to the resting state
      }
  });
  //touch events that enable animations
  window.addEventListener("touchstart", (event) => {
    event.preventDefault(); // prevent default browser action
    if (event.touches[0].clientX > window.innerWidth / 2) {
      // move right
      if (currentSpeed === 0) { // if at rest, go to walking
        mario.startWalking();
      } else if (currentSpeed === 3) { // if walking, go to running
        mario.startRunning();
      }
    } else {
      // move left
      mario.startPuffing();
    }
  });
  //stop animation on window blur
  window.addEventListener("blur", () => {
    mario.stopAnimate();
  });
  //start animation on window focus
  window.addEventListener("focus", () => {
     mario.startFlipping();
  });
  //start animation on page load or page refresh
  document.addEventListener("DOMContentLoaded", () => {
    // adjust sprite size for high pixel density devices
    const scale = window.devicePixelRatio;
    const sprite = document.querySelector(".sprite");
    sprite.style.transform = `scale(${0.2 * scale})`;
    mario.startResting();
  });
</script>

# Introduction

## Art 
I drew many things included in the game, such as the player spritesheet, main bedroom, and the main menu. Throughout the designing, we changed different styles and other parts to fit within our game. The art I made was mainly made for refrence and I think the end result was better than I could make. Some art I made that was in the game is below.

![Main](<images/Original Game/walking-sprite2.png>)

![Box 1](<images/Original Game/box1.png>)

![Box 2](<images/Original Game/box2.png>)

The above are just some of the more obvious ones you see first hand in the game, but i also did all the minigame art and more.

## Code 
I worked on OOP, the computer in the first minigame, the player, scrolling, and the monster interaction. I also made a refrence for how the game is supposed to look like. I then introduced audio and enviroment ambience. I did more such as the main menu and text engine. When coding, I found it vary useful to use objects. This is because it was like a storage of values of an image that we can manipulate without an excess of global variables.

## Objects

```
const object = {
    x: 0,
    y:0,
    height: 64,
    width: 32,
    img: new Image(),
    src: "/Group/images/Game/birdgame_bird.png"             
};
```
This line of code creates the new image then defines what the image is from our repository game images folder (in this case the bird png). It then creates the bird image in the first minigame. 
After this, it is named and the size can be set. It first asks for the size of the image file, then the size we want to draw it, then the placement in the game.

Objects can also be used for other things, such as player, text, or buttons. In the example below, we have an example of a text object that can be used and changed in the canvas.

```
const text = {
    x: 0,
    y; 0,
    space: 14,
    txt: "Hello World",
    font: "14px Arial",
};

ctx.font = text.font;
ctx.fillStyle = "black";
ctx.fillText(text.txt,text.x,text.y);
```
This text is defined in an object, and then can be drawn on the canvas. The text's peramerters are defined by the object variables of font, x, y, and spacing.

We can then add a custom text function to control even more of it.

```
function text(x,y,space,cutx,text) {
    var words = text.split(" ");
    var len = words.length;
    var textX = x;
    var textY = y;
    for (var letter = 0; letter < len; letter++) {
        ctx.fillText(words[letter], textX, textY);
        textX += space*words[letter].length;
        if (textX+(space+words[letter].length) > cutx) {
            textX = x;
            textY += 14;
        }
    }
};
```
The function above allows for cutoff of the text at a certain x on the canvas that prevents the text from moving past that x. When the function detects a word that is past the text, then it resets the text to the original starting x and changes the y of the text down by the spacing/font size. Below are some of the tests/examples of what I developed for the game.

[End Credits](/student//c4.1/2023/10/27/SimpilfiedEndCredits.html)

[Bird Game In Computer](/student//c4.1/2023/10/25/birdgame.html)

[Computer Minigame](/student//c4.1/2023/11/01/MinigameComputer.html)

[Text Engine](/student//c4.1/2023/10/23/textEngine.html)

[Object Demnstration](/student//plans/monster_smash)

[Game Story](/student//plans/Game_Project_Plan)

## Movement 
The original design for movement was slow and weird. So I designed a new one that uses a velocity of the player that changes its position rather than moving it by the position. This allowed for a more dynamic movment and friction.

```
// Determine which keys mean what
up = "KeyW"; 
down = "KeyS";
left = "KeyA";
right = "KeyD";
```
This determines which keys mean what functions (move left, right, up, down)
```
//handle keydowns(press key)
...
// Using Previously Defined Variables

case up:
    player.y += -1
    break;
case down:
    player.y += 1
    break;
case right:
    player.x += 1
    break;
case left:
    player.x += -1
    break;
```
Define what key does what and when. When you press the W key it move the character up, when you let go it stops, and same for "S" key. 
This meant that gravity had to be set to 0/deleted.

This ability to move up and down had the problem that the character could now walk on the wall, which we did not want. We decided to add a collision using this code:
```
function checkCollide(x, y, width, height) {
    var tx = object2.x - x;
    var ty = object2.y - y;
    if (Math.abs(tx) < width) {
        if (Math.abs(ty) < height) {
            return true;
        }
    }
};
```
The code above uses the distance of the objects and detects when there is an overlap between two objects. When combining movement and collision, we can make a working game.

## More Drawings

![Menu Layer 1](<images/Original Game/menu_tree.png>)

![Menu Layer 2](<images/Original Game/menu_building.png>)

![Menu Layer 3](<images/Original Game/menu_entities.png>)

![Menu Layer 4](<images/Original Game/menu_fade.png>)

# Reflection

Over the course of the trimester, I feel I have really grown and matured. I've gotten closer to people and made new friends. I've learned a few things too, such as two new coding languages, javascript and html, more about DOM and how computers function and how to make something interesting and fun with code. The most important thing I belive I have learned is how to run a group and reach a goal. The example here would be joining a group of 4 people and creating our horror game. We pushed each other to reach our full potential, and if we had a little more time, we could have made something that could probably be considered a full game.

Apart from teamwork, I feel I have been able to use my creativity with my coding, such as: creating a compact collision code, main menu, creating a story that's interesting, and much more.

In my group, my job was scrum leader. I, for the most part, dictated what our goal was and what the next steps should be. Our group would probably have spent more time on what to make rather than making a game if it wasn't for me. But I think my group also put in a lot of commitment and I appreciate them for that. Lastly, I think I could use some of my new skills in coding to create something that is far more advanced compared to our pretty advanced game, I just wish I had more time.
