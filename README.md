# **!Disclaimer: Following description is not finished.**

This demo shows the first scene of a game I am creating. It focuses on displaying a galaxy full of star systems that you can travel through by jumping from one to another to explore new planets.
The demo is available for download in the [release section.](https://github.com/ppietrasik/galaxy/releases)

![](https://i.imgur.com/KlVm9QS.png) 

## **Stars Generation - WIP**

Generated galaxy is made up of ~10000 * 255^2 * 78.5% = 510 446 250 different stars/solar systems, each solar system contains from 1 to 6 planets. That makes the total number of celestial objects in the galaxy exceed 3 billion.
Generation starts with the Poisson Disc sampling algorithm to obtain an evenly distributed list of discs(points with radius), which will also be a constraint to remain a minimum distance from each other.
The resulting list contains around 10000 samples, that will become our stars for the initial chunk. Later initial chunk will be replicated 255 times for each of the x&y axes, effectively producing a grid of chunks.

![](https://miro.medium.com/max/700/1*93EtzInFnl4hZNCeYoTEVg.png) 
[source](https://medium.com/@hemalatha.psna/implementation-of-poisson-disc-sampling-in-javascript-17665e406ce1)

The default Poisson Disc algorithm produces visible seam between chunks, to compensate for this algorithm was adjusted to produce tillable results.
There are many previous works related to adjusting the Poisson Disc for producing tillable results eg. [Recursive Wang Tiles](https://johanneskopf.de/publications/blue_noise/), mostly in a field of real-time stippling.
Problematically, most of them are rather sophisticated, to avoid unnecessary complexity, a simpler solution was chosen.
Changing the distance calculation of the algorithm to support the toroidal surface was proven to be good enough for this particular application.

Solar systems are guaranteed to each has a unique 32-bit unsigned integer id, by following this generation code: `(generationId << 16) | (chunkX << 8) | chunkY`

## **Stars Rendering - WIP**
A single star is being represented as one quad(4 vertices, 2 triangles). Color is being generated based on the star's temperature.
A custom vertex shader is applied to the star's mesh to transform it in a way that will always face the camera.

Rendering all 510 446 250 stars is not feasible for consumer-grade hardware. Because of chunk based approach, the number of stars can be easily limited by only rendering stars from selected chunks.
In this project, we are only interested in displaying stars from a single chunk in which the player/camera currently resides. Therefore, we can already optimize the number of stars to 10000, by taking into account only a single chunk(one chunk = ~10000, as explained before).
Which is not an unreasonable amount to process for the modern hardware.

To further decrease the number of the stars to render, we can select a certain distance from the player's position by which stars are omitted from the rendering process.
It can be achieved by just brute-forcing distance calculation for all the stars in the chunk, or by opting for one of the space-partitioning data structures to optimize spatial searches.
Quadtree was select for its simplicity, and its performance over the brute force solution.

Although, there are some cases where displaying only the stars inside of the chunk which player currently is in, would result in some unwanted behavior.
One of these cases occurs when a player approaches a chunk boundary. Displaying only one chunk means that the player will only see stars that are inside it. Stars from adjacent chunks will not be displayed until the player crosses the boundary.
A simple solution would be to also render stars from nearby chunks, but in the worst case, it would add 3 times the amount of processing. 
By adjusting the quadtree's search query algorithm to support the toroidal surface, we can eliminate the issue.

[WIP - Insert gif to explain] 
## **Planets Rendering - WIP**

## **Spiral Galaxy Rendering - WIP**

## **Background Rendering - WIP**

## **Screenshots**
![](https://i.imgur.com/n1OuRUU.png)
![](https://i.imgur.com/VxTqjvh.png) 
![](https://i.imgur.com/cSmoGmN.png) 
![](https://i.imgur.com/yQfLFXI.png)
![](https://i.imgur.com/bcvwDWP.png)
