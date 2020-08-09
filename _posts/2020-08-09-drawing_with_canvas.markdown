---
layout: post
title:      "Drawing with Canvas"
date:       2020-08-09 20:03:51 +0000
permalink:  drawing_with_canvas
---


When I started my javascript project, I decided I wanted to do some kind of game and decided to try my hand at making a hangman game. My biggest question after settling on this idea was how to draw the actual stickman. I did some googling and found out about a html canvas tag and it seemed like best way to go.

The first thing I needed to do was add a html canvas tag to my document. 

```

<canvas id="hangman-box"></canvas>

```

Pretty easy start! Next I had to be able to find that element in my javascript documents, so I assigned it to a variable.

```

const canvas = document.getElementById('hangman-box');

```

Next I did some more research on how to actually draw anything on the canvas. I found that the first thing I needed to do to start drawing was to use the getContext function to select what method I wanted to use. There are a few different types of accepted values, but '2d', which renders a two-dimensional drawing seemed like the best bet for my stickman. I added that to my code and assigned it to a variable as well.

```

const canvas = document.getElementById('hangman-box');
const ctx = canvas.getContext('2d');

```

The size of the canvas element that is generated is 150px wide and 300px tall. If you want to change the size it is better to use `width=` and `height=` in your html rather than adding width and height to your css. While adding the values to your css will change the size of the object, it will not respect the aspect ratio of the initial canvas and the drawings made will appear distorted.

After a bit more googling and reading, I found out that `<canvas>` only supports two shapes, rectangles, and paths, which worked out well for me, since I would mostly need to draw lines. Basically, I needed to provide a starting point and end point on the grid. The most confusing aspect was the trial and error involved in finding the right starting and ending points, because, unlike the grids I was accustomed to in math classes with (0, 0) at the center, the (0,0) point was in the top left corner. So the x value would increase moving down and the y value would increase moving to the right. 

To begin to draw you have to use the function beginPath, which creates a new path and all subsequent drawing commands are built upon it. Each time it is called it starts a new path and you begin drawing a new shape. To set the starting point you use moveTo(x, y) and for the end point you use lineTo(x, y). Then to actually draw the line I chose to use the stroke function. All of these functions are called on my `ctx` variable because it needs to know how to render the element. So my code to draw a straight line at the bottom of my canvas would be: 

```

const canvas = document.getElementById('hangman-box');
const ctx = canvas.getContext('2d');

cts.beginPath();
ctx.moveTo(0, 150);
ctx.lineTo(100, 150);
cts.stroke();

```

Great! So drawing lines seemed pretty straight forward and most elements of a stickman are lines, but what about the head?

For this, I discovered arc() that could be used to draw a circle in a very similar manner to drawing a path. This function takes a few parameters, x and y coordinates of the center point, a radius, a starting angle (in radians), an ending angle (in radians), and optionally a boolean value to indicate drawing direction (false is default and clockwise).  So my code for drawing the head looks like: 

```

ctx.beginPath();
ctx.arc(150, 25, 15, 0, 2*Math.PI);
ctx.stroke();

```

I knew that I would need to draw several elements (10 to be exact), so I decided to write a few functions to accomplish drawing the stickman and noose (a bit morbid, no?) in order to keep my logic as DRY (Don't Repeat Yourself) as possible.

```

// To draw each element

drawPart () {
  ctx.moveTo(startX, startY);
	ctx.lineTo(endX, endY);
	ctx.stroke();
}

// functions for each element
btmFrame = () => {this.drawPart(0, 150, 100, 150)}
leftFrame = () => {this.drawPart(0, 150, 0, 0)}
topFrame = () => {this.drawPart(0, 0, 150, 0)}
rope = () => {this.drawPart(150, 0, 150, 10)}
head = () => {
   this.ctx.arc(150, 25, 15, 0, 2*Math.PI)
   this.ctx.stroke()
}
torso = () => {this.drawPart(150, 40, 150, 100)}
leftArm = () => {this.drawPart(150, 60, 120, 30)}
rightArm = () => {this.drawPart(150, 60, 180, 30)}
leftLeg = () => {this.drawPart(150, 100, 130, 130)}
rightLeg = () => {this.drawPart(150, 100, 170, 130)}

// To render each element on the page 

drawHangman(lives) {
  const drawArr = [rightLet, leftLeg, rightArm, leftArm, torso, head, rope, topFrame, leftFrame, brmFrame];
  ctx.beginPath();
	ctx.strokeStyle = '#999' // refers to the color of the line drawn
	ctx.lineWidth = 2 // width of the line drawn
	drawArr[lives](); // execute the function in the drawArr at index=lives, where lives is the number of lives that you have left, which is another function outside of the scope of this blog post.
}

```

It was fun to figure out how to use canvas and there are a lot more things that can be done with it. I'm looking forward to learning more ways to use canvas and other animations that can be done with javascript, css, and html!
