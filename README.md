# point_in_polygon

Simple package to check if a Point (coordinates) is inside a polygon representing by a list of Point by using a Ray-Casting algorithm.

## Usage

```
final List<Point> points = <Point>[
  Point(y: 42.412328554181684, x: -71.61572554715048),
  Point(y: 42.382918132678284, x: -71.63254836209188),
  Point(y: 42.36617846481582, x: -71.5951261819161),
  Point(y: 42.39306121437432, x: -71.5680036843575),
];

final Point point = Point(x: -71.60473921902548, y: 42.38951132221119);
Poly.isPointInPolygon(point, points)  // true

final Point point2 = Point(x: -71.76850417263876, y: 42.38925775080263);
Poly.isPointInPolygon(point2, points) // false
```
## Expanation

Ray-Casting algorithm: https://en.wikipedia.org/wiki/Point_in_polygon
   
Example case
  
Let's  say we have a point and a polygon already, represented by this graph:
```  
     |
    5|
     |
     |
    4|                       * Vertice B (4, 4)
     |                      / \
     |                     /   \
    3|     * Point (1, 3) /     \
     |                   /       \
     |                  /         \
    2|                 /           \
     |                /             \
     |               /               \
    1|             * Vertice A (2, 1) \
     |              \                  \
     |               \                  \
   -----------------------------------------------------------
    0|     1     2     3     4     5     6     7     8     9
``` 
We will draw a horizontal line from Point and for each line between the vertices, 
we will figure out whether our horizontal line crosses that line. 
Then if the total number is even (or zero) after doing that calculation for all lines, 
we know the point is outside, if it is odd it's inside:
```
  
     |
    5|        * -----------------------------------------> 0 collisions, point is outside
     |
     |
    4|                       * Vertice B (4, 4)
     |                      / \
     |                     /   \
    3|     * -----------> /---->\----------> 2 collisions, even, point is outside
     |                   /       \
     |                  /         \
    2|                 /           \
     |                /    *------->\------> 1 collision, odd, point is inside
     |               /               \
    1|             * Vertice A (2, 1) \
     |              \                  \
     |               \                  \
   -----------------------------------------------------------
    0|     1     2     3     4     5     6     7     8     9
```
  
So if we don't have the lines, only the corners of the polygon how do we calculate the intersections?
 
`y = mx + b`

In this standard linear equation, M is the slope of the line and B is the y-intercept of the line. So if we rearrange it to solve for an X value (as we want to find the X value at which our horizontal line from our point would touch the edge, and whether that value is > the real X), we can insert our point Y value as Y and get our result if we have M and B for the line between two vertices.
  
In our example:
```
     |
    5|
     |
     |
    4|                       * Vertice B (4, 4)
     |                      / \
     |                     /   \
    3|     * Point (1, 3) /     \
     |                   /       \
     |                  /         \
    2|                 /           \
     |                /             \
     |               /               \
    1|             * Vertice A (2, 1) \
     |              \                  \
     |               \                  \
   -----------------------------------------------------------
    0|     1     2     3     4     5     6     7     8     9
```  
M is 1.5:
```
m = (aY - bY) / (aX - bX) 
m = ( 1 - 4 ) / ( 2 - 4 )
m = 1,5
```
B is -2:
```
b = ((aX * -1) * m) + aY
b = (( 2 * -1) * 1.5) + 1
b = -2
```

Therefore our intercept point is 3.33:
```
x = (pY - b) / m
x = (3 - -2) / 1.5
x = 3,33
```
3.33 is > our point's X val of 1, so this time the ray intercepts the line. 
We would do this calculation for all the other edges, and in our example we would have 2 intercepts, so Point is outside the polygon.
