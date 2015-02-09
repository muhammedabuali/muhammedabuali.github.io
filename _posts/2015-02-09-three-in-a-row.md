---
title: A solver for three-in-a-row-puzzle
layout: post
published: true
---

<br>
I was happy I got an A+ in the constraint programming course and I was bored, so I decided to make a solver for a puzzle. Although I found out later the grades were somehow inflated, I like to make stuff like that. In this post I will explain how I did a solver for an online puzzle. including the *javascript* code to read the puzzle and to post the answers back.
<br>
<br>

### The puzzle
the puzzle is called three-in-a-row you can play it online [here](http://www.brainbashers.com/3inarow.asp) . essentially, you have two fill the grid with white and blue blocks such that, no three consecutive blocks have the same color. besides, every row and every column should have an equal number of white and blue blocks.
<br>
![empty puzzle example](/images/puzzle-empty.png  "a photo for an empty puzzle")
<br><br>


### The code
* input code: a *javascript* code to read the locations of filled blocks
* constraint solving: a *python* code to find the solution
* output code: a *javascript* code given the solutions it fills the grid with the right blocks. we don't want to click on those blocks manually do we?
<br>
<br>

### The input
I wrote a simple *javascript* code that loops over the table cells and produces two arrays, **whites** and **blues**. the arrays contain the locations of the blue and white blocks and are later printed through the browser javascript console

{% highlight javascript %}
my_table = document.getElementsByClassName('inarowtable')[0]

whites=[]
blues=[]
for (var i=1; i< my_table.rows.length; i++){
 var base = (i-1) * (my_table.rows.length-1);
  for (var j=0; j< my_table.rows[0].cells.length-1; j++){
    var cell = my_table.rows[i].cells[j];
    var class_name = cell.className;
    if (class_name.indexOf("white") >-1 ){
      whites.push(base+j+1);
    }else if (class_name.indexOf("black") >0 ){
      blues.push(base+j+1);
    }
  }
}
console.log(whites.toString());
console.log(blues.toString());
{% endhighlight %}

<br>
<br>

### The solver
I use the [or-tools library](https://code.google.com/p/or-tools/) for the solver. it is
written in *C++* and has bindings for *python* which of course is usually easier to code in. you need to install the *ortools* python package to run the solver code
<br>
for those of you unfamiliar with [constraint programming](http://en.wikipedia.org/wiki/Constraint_programming), it is a paradigm concerned with assigning values to variables by shrinking their domain using added constraints and then searching through remaining domain for possible solutions.

#### The variables
agrid of N x N variables where N is the width of the table. each cell could either be *blue* or *white*  so, I represent it with a 0 or 1.

#### The constraints
the constraints are very easy to express 

1. no three cells of the same color in a row, so the sum of every three consecutive cells cannot be zero nor three
2. equal number of whites and blues in all rows and columns, so the sum of every row and column is exactly seven

#### The input
the input is simply the size of the grid and the blue and white arrays we got earlier
<br>

#### The code

{% highlight python %}
from ortools.constraint_solver import pywrapcp


def main(dimension, whites, blues):
    solver = pywrapcp.Solver("three in a row")

    line = range(0, dimension)
    grid = {}

    for i in line:
        for j in line:
            grid[(i, j)] = solver.IntVar(0, 1)

    # set whites and blues
    for i in whites:
        solver.Add(grid[((i - 1) / dimension, (i - 1) % dimension)] == 0)

    for i in blues:
        solver.Add(grid[((i - 1) / dimension, (i - 1) % dimension)] == 1)

    # vertically three in a row
    for i in range(0, dimension - 2):
        for j in line:
            solver.Add(
                (solver.Sum([
                    grid[(i, j)],
                    grid[((i + 1), j)],
                    grid[((i + 2), j)]]
                ) % 3)
                > 0)

    # horizontally three in a row
    for j in range(0, dimension - 2):
        for i in line:
            solver.Add(
                (solver.Sum([
                    grid[(i, j)],
                    grid[(i, (j + 1))],
                    grid[(i, (j + 2))]]
                ) % 3)
                > 0)

    # every row equal blue and white
    for i in line:
        solver.Add(solver.Sum([grid[(i, j)] for j in line]) == dimension / 2)

    # every col equal blue and white
    for j in line:
        solver.Add(solver.Sum([grid[(i, j)] for i in line]) == dimension / 2)

    all_vars = [grid[(i, j)] for i in line for j in line]

    vars_phase = solver.Phase(all_vars,
                              solver.INT_VAR_SIMPLE,
                              solver.INT_VALUE_SIMPLE)

    solution = solver.Assignment()
    solution.Add(all_vars)
    collector = solver.FirstSolutionCollector(solution)

    # And solve.
    solver.Solve(vars_phase, [collector])

    if collector.SolutionCount() == 1:
        for i in line:
            print [int(collector.Value(0, grid[(i, j)])) for j in line]


if __name__ == '__main__':
    main(14,
         [17, 22, 24, 34, 38, 49, 50, 55, 57, 58, 60, 64, 70, 82, 84, 90, 92, 93, 104, 142, 145, 146, 149, 151, 161,
          169, 174, 181, 182, 183],
         [2, 5, 7, 19, 26, 42, 47, 66, 72, 96, 99, 102, 111, 121, 124, 125, 158, 168, 179, 190])
{% endhighlight %}
<br>
<br>

## The output
Now we have made the solver and we can get the zeros and ones that would solve the puzzle and we want to insert them in the puzzle. but those puzzles are big and it takes time to fill all the cells, why not make another javascript code to click on the blocks.
<br>
this looks like a nice idea, but javascript doesn't make that easy. fortunately, [JQuery](http://jquery.com/) does. so I load JQuery into the page and then given the solution grid I fill the blocks
<br>
<br>

### The code
{% highlight javascript %}
var jq = document.createElement('script');
jq.src = "https://ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js";
document.getElementsByTagName('head')[0].appendChild(jq);

solution = [[0, 1, 0, 0, 1, 0, 1, 1, 0, 1, 1, 0, 0, 1],
[1, 1, 0, 0, 1, 1, 0, 0, 1, 0, 0, 1, 1, 0],
[1, 0, 1, 1, 0, 0, 1, 1, 0, 0, 1, 0, 0, 1],
[0, 1, 0, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 1],
[0, 0, 1, 0, 1, 0, 1, 0, 1, 1, 0, 1, 1, 0],
[1, 1, 0, 1, 0, 1, 0, 1, 0, 0, 1, 0, 1, 0],
[0, 1, 1, 0, 1, 0, 1, 0, 0, 1, 0, 1, 0, 1],
[1, 0, 1, 1, 0, 0, 1, 0, 1, 0, 1, 0, 1, 0],
[1, 0, 0, 1, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0],
[0, 1, 0, 0, 1, 1, 0, 1, 0, 1, 1, 0, 0, 1],
[1, 0, 1, 1, 0, 0, 1, 0, 0, 1, 0, 1, 1, 0],
[1, 0, 0, 1, 0, 1, 0, 1, 1, 0, 1, 0, 0, 1],
[0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 1, 1, 0, 0],
[0, 0, 1, 1, 0, 1, 0, 1, 0, 1, 0, 0, 1, 1]];

for (var i=1; i< my_table.rows.length; i++){
  var base = (i-1) * (my_table.rows.length-1);
  for (var j=0; j< my_table.rows[0].cells.length-1; j++){
    var cell = my_table.rows[i].cells[j];
    var class_name = cell.className;
    if (class_name.indexOf("grey") >-1 ){
      $(cell).trigger('mouseup');
      if (solution[i-1][j] == 0){
        $(cell).trigger('mouseup');
      }
    }
  }
}
{% endhighlight %}
<br>
<br>

## Summary
in this blog post, I explained how I worte a solver for an online puzzle using *python* and how jacascript can be used. If you have read this far then congratulations! let me know what you think in the comments.