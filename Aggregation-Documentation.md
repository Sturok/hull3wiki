# Polygon Aggregation

Our hull aggregation implementation is loosely based of the approach described by authors Xie Zhong, Ma Lina, Wu Liang. This approach is described in the paper titled Polygon Aggregation Algorithm Using the Simple Feature Model. The full paper can be found [here](https://ieeexplore.ieee.org/abstract/document/5630214?casa_token=2vwW40IUGv4AAAAA:4KH_4iJ9pHC59v-HSHgD9VOW8fHdOyMTuQFPRou9MLFJ0b-fYsIghtnuW-U4oAB7e5tkifJ67Qxe)

This approach assumes that line segments are ordered in a clockwise fashion i.e the end point of a line segment is the start point of the next line segment in the list. Clipper typically does this automatically within reason. An example where it may break is if a single hull has an internal hole.

## Remove shared edges

This algorihim takes two polygons, A and B, and determines the regions where the line segments are shared.

The algorithm can be described as a 4-step process.

- Step 1: Determine if the bounding box of B intersects with a line segment in A. If an intersection is found go to step 2.

- Step 2: Determine if the bounding box of any line segment in B intersects with the line segment of A found in Step 1. We also want to determine if the two-line segments intersect. If both conditions are true go to Step 3.

- Step 3: Remove line segments from A and B found in Step 1 and Step 2 respectively. Move onto the next line segment in A. Return to Step 2

- Step 4: Repeat Step 1 until all line segments in A have been checked.

The following pseudocode describes the basic algorithm

```C++
function removeSharedEdges(Polygon A, Polygon B)
{
    Calculate bounding box of B

    while (i < A.size) 
    {
        Calculate bounding box of line segment A[i,i+1]

        if ( Bounding box B and boundng box A[i,i+1] intersect )
        {
            while (j < B.size()) 
            {
                Calculate bounding box of line segment B[j,j+1]

                if ( Bounding box A[i,i+1] and boundng box B[j,j+1] intersect & Line segment A[i,i+1] and B[j,j+1] intersect){

                    Remove shared edge within merge threshold
                    i = i + 1

                }
                else 
                {
                    j = j + 1
                }
            }
        }
        else 
        {
            i = i + 1
        }
    }
}
```

## Calculate bounding box

The function to calculate the bounding box finds the maximum and minmium x,y coordinates. These form the corners of the bounding box. Additionaly at the end of the function the merge threshold is added to each coordinate. This increases the size of the bounding box and hence, it will intersect with more items even if they don't exactly fall within the bounds of the coordinates.

## Bounding box intersection

Adam

## Line segment intersection

Adam

Next: [Part 6: Hull Simplification](./Hull-Simplification.md)\
Home: [Home](./Home.md)