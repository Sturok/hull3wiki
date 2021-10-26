# ASSIMP for importing 3D scenes
ASSIMP is a library to import 3D scenes from a couple of different file types.  A full list of supported file types is available at [ASSIMP Documentation](https://assimp-docs.readthedocs.io).\
The data is imported into a aiScene object which contains much more information than needed to generate a 2D hull. We do not need information on the scene lighting, textures, materials, bones or animations present.\
ASSIMP is focused for 3D rendering so imports multiple copies of geometry based on different aspects of the scene. Only 1 copy of the 3D geometry is needed to generate a Hull, so there are pre-processor flags that can be used to limit the data that is imported.

```cpp
Assimp::Importer importer;
importer.SetPropertyInteger(AI_CONFIG_PP_RVC_FLAGS, aiComponent_NORMALS | aiComponent_TEXCOORDS);

const aiScene* scene = importer.ReadFile(pFile, aiProcess_RemoveComponent | aiProcess_JoinIdenticalVertices);
```
The above code removes the data that describes the normals and texture coordinates and also tries to identify and join identical vertices if present.\
\
The scene object is not a mutable object. ASSIMP allows us to make the scene data mutable in a process called _orphaning_.
```cpp
aiScene* scene1 = importer.GetOrphanedScene();
```
# Flattening geometry along an axis
In order to create a 2D hull representing a flattened 3D object we must flatten the 3D scene along an axis.\
Inputs: 3D mesh data, axis to flatten\
Ouputs: data representing flattened shapes from the mesh.

```cpp
ClipperLib::Paths flattenMesh(aiMesh* mesh, Axis projectionAxis);
```
HULL-3 uses the Clipper Library described in Part 4 of this documentation to generate a hull. This library has a data structure which is a list of 2D polygons/shapes (the library calls this a Path) called Paths.\
The FlattenMesh function outputs a list of polygon shapes in the data type that the Clipper Library will need to generate the hull.
\
Within eah mesh object, each shape is represented by a face on the 3D object. Each face is described by the vertices that make the shape. 3D geometry is made up of connected faces, a face can share vertices with another face.\
```cpp
ClipperLib::Paths flattenMesh(aiMesh* mesh, Axis projectionAxis) {

ClipperLib::Paths shapes;
//for each face in the mesh
    for (unsigned int i = 0; i < mesh->mNumFaces; i++) {
        aiFace face = mesh->mFaces[i];

        // clipper uses a Path which is a list of points defined by X,Y as integers
        ClipperLib::Path tempShape;

        //for each vertex in face
        for (unsigned int j = 0; j < face.mNumIndices; j++) {
            int index = face.mIndices[j];
            aiVector3D point = mesh->mVertices[index];
            
            ClipperLib::IntPoint temp;

            //we want 2D points so our end X and Y depends which axis we are discarding
            if (projectionAxis == Axis::X) {
                temp = convertToIntPoint(point.y, point.z);
            }
            else if (projectionAxis == Axis::Y) {
                temp = convertToIntPoint(point.x, point.z);
            }
            else if (projectionAxis == Axis::Z) {
                temp = convertToIntPoint(point.x, point.y);
            }
            tempShape.push_back(temp);

        }

        //check if we have flattned a shape into a line and then don't add it if we have
        if (tempShape[0] == tempShape[1] || tempShape[0] == tempShape[2] || tempShape[1] == tempShape[2]) continue;

        //sort them so that the lowest x,y comes first, then make sure they go in the right direction (ie. clockwise vs couinterclockwise)
        //the clipper Library doesn't allow us to easily sort the points in the Path ie. a path should be in a given order for a  polygon
        //BUT because we are using triangles its OK to just sort the list of points and it won't change the shape
        std::sort(tempShape.begin(), tempShape.end(), [](ClipperLib::IntPoint a, ClipperLib::IntPoint b) {
            if (a.X != b.X) return a.X < b.X;
            else return a.Y < b.Y;
        });

        //Each point in the triangle - already sorted so that o1 will have the lowest x,y
        ClipperLib::IntPoint o1, o2, o3;
        o1 = tempShape[0];
        o2 = tempShape[1];
        o3 = tempShape[2];

        //The slope/gradient between 2 points in the triangle
        double s1 = DBL_MAX;
        double s2 = DBL_MAX;

        //if o2 is not above o1 then we calculate the slope between them
        if ((o2.X - o1.X) != 0) {
            s1 = (o2.Y - o1.Y) / (double)(o2.X - o1.X);
        }

        //if o3 is not above o1 then calculate the slope between them
        if ((o3.X - o1.X) != 0) {
            s2 = (o3.Y - o1.Y) / (double)(o3.X - o1.X);
        }

        // points have rounded to be colinear then we don't want to add this shape to clipper
        if (s1 == s2) continue;

        //if not the correct direction then swap the second and third point so they go in the right direction
        if (!((o3.X - o1.X) == 0 || s2 > s1)) {
            ClipperLib::IntPoint temp;
            temp = tempShape[2];
            tempShape[2] = tempShape[1];
            tempShape[1] = temp;
        }

        //add the constructed Path to the Paths
        shapes.push_back(tempShape);
    }
    
    //return the Paths that will be used by clipper
    return shapes;
}
```

Next: [Part 4: Polygon Union/Clipping and Hull Generation](./Polygon-Union-With-Clipper.md)\
Home: [Home](./Home.md)