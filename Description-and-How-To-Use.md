# Project Description
This Hull-3 application is designed to output 2D geometry representing the outline of the shadow of a complex 3D shape (the shape’s ‘hull’). The output includes 2D line segments describing the outline and also defining any internal holes present.

The input is a 3D object/scene. This may be multiple 3D meshes that fit together like a jigsaw. These meshes are imported, combined, and flattened along one of the primary orthogonal 3D planes i.e. X, Y and Z. If a 3D object were flattened in the X axis then its geometry would be represented in the YZ plane.

The user is initially presented with a dashboard from which they can import the mesh(es), select the axis to flatten along, and decide whether to use GPU acceleration or not (though in the current version, the GPU acceleration button does nothing and GPU acceleration is never used).

The Hull-3 application then passes this information through a C#/C++ interface to the main code in C++. 3D scene importation, mesh flattening, hull generation, and hull aggregation are performed in C++. Multithreading is used on these processes to increase efficiency. A final list of line segments representing a final hull is sent back to the dashboard to display to the user.\
\
![Image of Application Flow](https://drive.google.com/uc?id=1AMX5ixXX184slfDJBkqT0huMFTEEPh61)

# How to use the Hull-3 Application
You are initially presented with a splash screen. Click on the screen to move onto the main screen\
![Image Of splash screen](https://drive.google.com/uc?id=1UshjTL0bzcYXyedqjhCguwyax461yQVK)\
![Image of main screen](https://drive.google.com/uc?id=1b_ALlwapuMaVF3TQFUhdsmOp0zOrYFk-)\
\
**Step 1**: Select an axis to flatten by selecting the X, Y or Z button. Only one axis can be selected.\
**Step 2**: Select whether to use GPU acceleration.\This button currently does not affect the behaviour of the application and no GPU acceleration is used.\
_Steps 1 and 2 can be done in any order and are not final until step 3 is completed._\
**Step 3**: Select the model(s) to load - once a selection is made the application generates the 2D Hull based on the files selected.\
\
Below is an example of a 3D file loaded into the Hull-3 application\
![Image of 3D example](https://drive.google.com/uc?id=1761hzNkowqQ0mzw65RBAAdlR7Ei5sXli)\
\
The next 3 images are the Hull Output flattened in the X,Y and Z axis respectively.
![image of x flattening](https://drive.google.com/uc?id=1fSZe3etHipvweCdxa453itJamBxF-MVg)
![image of y flattening](https://drive.google.com/uc?id=1p-xuV6vjljQNeuMz34dMFzHvsa4fJ2TF)
![image of z flattening](https://drive.google.com/uc?id=1ZXbeFKBmJeq9MD_95lTwLSWhsrvFs5mu)\
\
Next: [Part 2: C#\C++ Interface](./CPlusPlus-Interface.md)\
Home: [Home](./Home.md)