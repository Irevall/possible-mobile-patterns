### Count possible patterns (in mobile style lock)
##### With full description of how I attempted to solve the problem

#### Full task overview:

![Task example](./readme/task-example.png?raw=true)

This is an example of a typical pattern lock on a mobile device.
It follows certain rules:
- any point can be a starting point
- pattern needs at least a single connection (so at least 2 points)
- each point can only be used once

Connecting the points can happen in multiple ways:
- horizontally (like A to B)
- vertically (D to G)
- diagonally (I to E or B to I)
- no point in between can be skipped, unless it was already used, so G to C is possible only if E has already been used

Actual task?
Write a function countCodes(start, length) that returns how many possible patterns exist, matching those parameters.


#### Step 0 - first thoughts and observations
My first thought was that it's extremally trivial to describe the problem (and the solution) to another human, but
I have literally no idea how to represent connection rules in technical terms.

My second thought was this:

![Step 0 representation](readme/step-0-highlight.png?raw=true)

In 3x3 grid only those highlighted points matter, because those are symmetrical axis:

![Step 0 axis](readme/step-0-axis.png?raw=true)

So, if staring in point A results in X combinations, so does starting in point C, G and I and so on.

My other observation was that if I somehow wrote down dependencies that occur in connections, for example:
- A can connect to B, A can connect to C if B is already used

I could easily use it in the same fashion, mirror it through every axis and from A dependencies I can have all 3 other.

#### Step 1 - representation
2 things occured to me, first of all, since I still had no idea how to write dependencies and given that it was just a 3x3 block,
I could just write it out manually and it would probably be the quickest solution. So I could just write out:
- Starting point
- Finish point
- Necessary points in between to make that connections

For example:
- A, B, null, as no points inbetween are necessary
- A, C, B, as point B needs to be already used to make A to C connection

Secondly, if I want to use symmetry, some math could be used, specifically coordinates, so:

![Step 1 coords](readme/step-1-coords.png?raw=true)

Now, about scaling up...
It's not mentioned at all in the task, but I figured out that it might be great idea if I want to shine and it shouldn't be that hard...
if I figure out all "logic" behind this.

#### Step 2 - scaling up and logic
So, let's say we aren't talking about 3×3 grid anymore, but X×X or even X×Y.

1 - Coming back to previous picture with axis of symmetry, if we want to accept custom grids, including X×Y, we reduce our axis
from 4 to 2:

![Step 2 axis](readme/step-2-axis.png?raw=true)

2 - I finally realized how to find point dependency.

![Step 2 dependency](readme/step-2-dependency.png?raw=true)

Given point A [xa, ya] and B [xb, yb] find their delta [dx, dy]. If they have a common divisor larger than 1, that means
there is at least a single point between them. So, for example our delta is [6, 3], common divisor 3, giving us [2, 1],
which, going from either point gives us [2, 1] and [4, 2].

#### Step 3 - actual code (inefficient)

1. Get X and Y to create a grid
2. Find starting points (top left quarter), as in each point [a, b] where a <= | (X + 1) / 2 |, b <= | (Y + 1) / 2 |
3. For each starting point, write out their dependency to every other point on the grid, including points directly between
4. Flip starting points and its dependencies to cover every point 
5. Remove repeated entries
6. Get [x, y] and number of moves between 2 and X * Y from user
7. Resursively check possible moves down the tree