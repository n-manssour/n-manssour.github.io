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

### 3. Description of the content of each class :

main: svgTest creates a Window (a window performs the requested work on a given image and using a given time).

Window class: to create our Graphical Interface (window), it allows to retrieve the time given by the user, and the SVG file with all its path (thanks to the SVG class) and create the new SVG file. At the end of the image processing we can immediately ask for the processing of another image as long as we don't close the window.

Svg class: class dealing with the processing of an SVG file

Point class: defines a point with two coordinates, it contains all the functions functions related to points such as creation, translation, construction, rotation construction, rotation (with respect to a center and an angle), calculation of distance between two points, or a Bezier curve point according to the parameters required.

Plate class: creates a plate and takes care of the functions related to the plates

Polygon class: creates a polygon using its list of Points, it contains the polygon's rotation and translation functions (which themselves call the Point's functions of rotations and translations of a Point), we also find the initialization function of the number of plates, number of joints/cuts (which also calculates the size of the cuts)

Path class: path management




### 4. Main algorithms:

- Polygon discretization: Initialization of the list of discrete points of the polygon, using the Bézier curve equation to discretize the Bezier curves, and using the segment equations to discretize the segments.

- Polygon translation: Translation of all points in the polygon's point list, and of the polygon's discrete point list.

- Polygon rotation: Rotation of all points in the polygon's point list, and in the polygon's discrete point list, according to the center of gravity of the polygon.

- Computation of the number of plates to cut: Go through the list of discrete points of the polygon, computing for each point the plate where it is located and add this plate to a list of plates, if and only if the plate is not already present in this list. Then final count of the number of plates in the list.

- Computation of the number of cuts: We go through the list of discrete points 2 by 2. Each pair forms a small segment. We check if the segment is cut by a mesh of the grid. If it is the case we calculate the intersection point. And add it to a list of vertical or horizontal intersections depending on the nature of the cut. In the end we have the list of intersection points of vertical cuts and the list of intersection points of horizontal cuts. We add the size of these lists together and divide the whole by two to obtain the number of cuts.

- Calculating the total size of the cuts: Using the two lists previously obtained, it is easy to calculate the total size of the cuts. We sort each list as required, grouping the pairs of intersection points of the same cut together. And we calculate the distance between them. This distance is added to a total that is finally returned.

- Genetic algorithm: Using a DNA composed of three genes (X shift, Y shift, angle) we created a random starting population of 100 DNA strands. We calculate for each strand the number of plates to cut, the number of cuts, and the total size of the cuts. Then we assign a score to each strand according to the results it has had. Knowing that the number of plate is more coefficient than the number of cuts, which is itself more coefficient than the total size of the cuts. We finally sort this population by order of increasing score. It is from now on that the reproduction cycles can begin. Using the reproduction of the best strands (elitism) we create 30 new strands that will replace the 30 worst strands. The reproduction uses cross-over and mutation. The new population is scored and sorted in order of increasing score, and the cycle is repeated. There are as many cycles as the time given by the user allows. At the end of the time, the best DNA is used to create the most optimally placed polygon.




For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).
