---
published: false
title: "A solver for three-in-a-row-puzzle"
---

<br>
  
I was happy I got an A+ in the constraint programming course and I was bored, so I decided to make a solver for a puzzle. Although I found out later the grades were somehow inflated, I like to make stuff like that. In this post I will explain how I did a solver for an online puzzle. including the *javascript* code to read the puzzle and to post the answers back.
<br>

### The puzzle
the puzzle is called three-in-a-row you can play it online [here](http://www.brainbashers.com/3inarow.asp) . essentially, you have two fill the grid with white and blue blocks such that, no three consecutive blocks have the same color. besides, every row and every column should have an equal number of white and blue blocks.
![empty puzzle example](/home/muhammed/workspace/web/muhammedabuali.github.io/images/puzzle-empty.png  "a photo for an empty puzzle")
<br>

### The code
the code consists of three parts:
- input code: a javascript code to read the locations of filled blocks
- constraint solving: a python code to find the solution
- output code: a javascript code given the solutions it fills the grid with the right blocks. we don't want to click on those blocks manually do we?


