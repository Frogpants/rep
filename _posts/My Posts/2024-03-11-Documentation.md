---
title: Documentation
search_exclude: true
courses: {'csse': {'week': 0}}
type: ccc
---


# Introduction

## Art 
I drew many things included in the game, such as the player spritesheet, main bedroom, and the main menu. Throughout the designing, we changed different styles and other parts to fit within our game. The art I made was mainly made for refrence and I think the end result was better than I could make. Some art I made that was in the game is below.

![Main Character](<../images/Original Game/walking-sprite2.png>)

![Box 1](<../images/Original Game/box1.png>)

![Box 2](<../images/Original Game/box2.png>)

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

![Menu Layer 1](<../images/Original Game/menu_tree.png>)

![Menu Layer 2](<../images/Original Game/menu_building.png>)

![Menu Layer 3](<../images/Original Game/menu_entities.png>)

![Menu Layer 4](<../images/Original Game/menu_fade.png>)

# Reflection

Over the course of the trimester, I feel I have really grown and matured. I've gotten closer to people and made new friends. I've learned a few things too, such as two new coding languages, javascript and html, more about DOM and how computers function and how to make something interesting and fun with code. The most important thing I belive I have learned is how to run a group and reach a goal. The example here would be joining a group of 4 people and creating our horror game. We pushed each other to reach our full potential, and if we had a little more time, we could have made something that could probably be considered a full game.

Apart from teamwork, I feel I have been able to use my creativity with my coding, such as: creating a compact collision code, main menu, creating a story that's interesting, and much more.

In my group, my job was scrum leader. I, for the most part, dictated what our goal was and what the next steps should be. Our group would probably have spent more time on what to make rather than making a game if it wasn't for me. But I think my group also put in a lot of commitment and I appreciate them for that. Lastly, I think I could use some of my new skills in coding to create something that is far more advanced compared to our pretty advanced game, I just wish I had more time.