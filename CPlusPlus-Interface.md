# C#\C++ Interface

The C++ library is available to other languages through the C ABI. The interface for this is defined in `HullGenerator/HullGenerator.hpp`. This exposes three main entities: the `Axis` enumeration, the `LineSegment` structure, and the `generateHull` function. 

## `Axis`
This enumeration has 3 possible values:
| Value | Meaning              |
|-------|----------------------|
| x     | Project along x-axis |
| y     | Project along y-axis |
| z     | Project along z-axis |

## `LineSegment`

### Structure definition
```c
struct LineSegment {
	float startX;
	float startY;
	float finishX;
	float finishY;
};
```

## `generateHull`
This function takes a list of object files and a number of options and returns an aggregated and simplified hull. There are additional parameters for selecting the projection axis, whether to use GPU acceleration (not currently available), and the merge threshold.
```c
std::vector<LineSegment> generateHull(const std::vector<std::string> & filePaths, Axis axis, bool useGPU, float mergeThreshold);
```
An example of how to use this can be found in `Applications/SolidSimplification/HelperMethods/HullGenerator.cs`

Next: [Part 3: File Importation and flattening](./File-Importation-And-Flattening)\
Home: [Home](./Home)