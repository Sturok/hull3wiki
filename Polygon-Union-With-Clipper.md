# Hull Generation through Polygon Union

## Hull Generation Summary
Starting with a collection of 2D shapes/polygons in a single plane, the union of all polygons is found. This union will then be a series of edges that defines the outside shape and any inner holes. This process has found the outer 2D hull and inner holes present.

## Polygon Union
The union of 2 polygons is the polygon that results from merging 2 polygons together, ignoring any internal edges or vertices.\

<img src="https://drive.google.com/uc?id=1C0PY_DyDX04_eY10BqaSqNzluvVILy7r" alt="Example polygon union" width="400"/>

In its simplest form across a group of polygons defining a larger piece of flattened 3D geometry, a single polygon defining the outer shape is created. This outer shape is the 2D Hull of the geometry.

<img src="https://drive.google.com/uc?id=1rYQXHsK1FKsh6Qt6EX6xvtABm_9xHYf-" alt="Simple polygon to HULL">

In a more complex form, ie. where there are internal holes in the group of polygons, then the output is multiple groups of line segments. Each group of connected line segments is in itself a polygon, but each group will define either an outer shape or an inner hole.

<img src="https://drive.google.com/uc?id=1R6zZw-sFiL_23QApADqaMUY8DqZJRrwl" alt = "Polygon with HOLE to HULL">

## Polygon Clipper Library
Polygon clipping is an operation that can be used to get the result of multiple boolean operations on polygons, including the union.\
\
Clipper is a library that allows computationally efficient union of a pair of polygons or a group pf polygons. Before putting polygons into the algorithm, they need to be sorted and are of the same orientation. \
\
Sorting polygons by their x|y coordinate, places spatially nearby polygons nearby in the list so that the algorithm is not trying to merge together two polygons that do not share on edge or overlap.\
\
Polygons vertices must all be listed in the same orientation as the algorithm assumes this to build up a result polygon. The Vatti Clipping algorithm on which the Clipper Library is built off, creates polygons in an ANTICLOCKWISE direction, so having all input polygons in an ANTICLOCKWISE direction means that the algorithm can iteratively work across a list of input polygons. This is an assumption of the input and unexpected results are obtained if polygons are not processed properly prior to using the Clipper Library.

### Clipper Library Implementation

```cpp
void generateIndividualHull(const std::string& file, Axis axis, Hull& hull)
```
generateIndividualHull outputs the singular HULL for all meshes in the provided file, flattened allong a provided axis.

Data Types
```cpp
    //the clipper object that does the work
    ClipperLib::Clipper clipper;

    // the assimp scene object
    aiScene* scene = ImportScene(file);

    //Paths is a clipperLib struct that is a list of Path
    //a Path is a list<IntPoint>
    //an IntPoint is a point in 2D space with coordinates as integers
    ClipperLib::Paths currentHull;
```
For each mesh in the assimp scene:
\
Flatten the polygons/shapes present int the mesh along provided axis.\
Prepare the shapes for ClipperLib.
```cpp
        ClipperLib::Paths shapes = flattenMesh(mesh, axis);
```
Add these shapes to the clipper object. They have already been sorted and are all of same orientation.
```cpp
        clipper.AddPaths(shapes, ClipperLib::ptSubject, true);
```
Run the Clipper Library to output the Union of the polygons, add the output to the currentHull object.
```cpp
        clipper.Execute(ClipperLib::ctUnion, currentHull, ClipperLib::pftPositive, ClipperLib::pftPositive);
```
LineSegments are the required output type to be passed back out through the C#/C++ interface.\
Each shape in the currentHull will either represent an external outline OR an internal hole.
For each of these shapes convert them to line segments and add to a total list of line segments representing the HULL.

```cpp
    for (const auto& path : currentHull) {
        for (int i = 0; i < path.size() - 1; i++) {
            hull.push_front(convertToLineSegment(path[i], path[i + 1]));
        }

        //link the last point to the first point to close the shape
        hull.push_front(convertToLineSegment(path[0], path.back()));
    }
```

Next: [Part 5: Hull Aggregation](./Aggregation-Documentation)\
Home: [Home](./Home)