## Genetic algorithm for industrial cuttin

**Project description:** For this project a customer in the industry needed a solution to optimize the cutting of figures in wood panels (1m x 1m). I did a state of the art of what could be done. I finally opted for the implementation of a genetic algorithm.

### 1. Initial situation
The client uses InkScape to generate its figures and simulate their placement on the cutting grid.
A figure consists of a polygon composed of segments and/or Bézier curves.
The grid is simply an InkScape grid with 1000mm spacing. All this is finally stored in an SVG format.

### 2. Implementation choices and ambiguity removal:

To realize this project, I decided to proceed step by step. Indeed, I first tried to extract the image "path" as a string, using an XML document parser. I then split this "path" into commands and points associated with these commands. Following this splitting, I was able to generate a list of points with absolute coordinates representing our polygon. The points are either ordinary points or control points (from Bezier Curve). It is on this list of points that the rest of the project is based. From this list of points, I was able to build a list of discrete points. Indeed, we have discretized the Bezier curves and even the segments. The distance between two neighboring discrete points is such that it is impossible for a plate or a cutout to be missed. The discretization is quite precise. This increases the computation time a little bit but makes the implementation of the code much easier.
I apply translations and rotations to these two lists characterizing the polygon. And it is from the list of discrete points that I am able to calculate the number of plates to be cut by the machine (plates on which the contour of the polygon passes), as well as the number of cuts and the size of these cuts. I decided to only count the number of plates to be actually cut by the machine rather than the total number of plates present in the polygon, because a plate entirely included in the polygon is optimally used and will not be cut by the machine, so we might as well not count it.
In order to find a better solution, I opted for a genetic algorithm. With cross-over and mutations at each reproduction (I wanted to make occasional mutations as explained in some sources, but after several trials, it turned out that it was more efficient to make mutations after each reproduction). The algorithm aims at elitism, in order to find a good result quickly. When the time given by the user is over, we create the SVG image of the best solution obtained, by creating a new SVG file, a true copy of the file to be processed. The only exception is that the "path" is replaced by the "new path".
For the grids, I model them directly in Inkscape, taking care to place the origin of the frame (indeed the frames in Inkscape and XML are not the same). The spacing between each mesh of the grid is 1000mm or 1 meter. It is this specific spacing that is used in the calculations to detect the plates to be cut and the cuts.
For the user all this is presented in the form of an interface. Where he will have to specify a desired time limit, which he will confirm. Then he will choose an SVG file on which to run the program. The visual of the starting and ending image is done on Inkscape
For more information please refer to the commented code.

### 3. Support the selection of appropriate statistical tools and techniques

<img src="images/dummy_thumbnail.jpg?raw=true"/>

### 4. Provide a basis for further data collection through surveys or experiments

Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. 

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
