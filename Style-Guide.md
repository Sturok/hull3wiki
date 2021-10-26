# C++

**Indentation style:** curly bracket on same line, indent = four spaces
```cpp
if (condition) {
    doAThing();
}
```

**Comments:** space after `//`, avoid comments on same line as code
```cpp
// The below line does a thing.
doAThing();
```

**Variable/function/etc names:** UpperCamelCase or lowerCamelCase
* UpperCamelCase: file names, classes
* lowerCamelCase: objects, methods/functions, attributes/variables

**Layout of code:**
* Avoid getters/setters unless there's a specific reason for them - prefer public attributes
* Don't use C strings or C arrays unless necessary
* Don't use raw pointers unless absolutely necessary - prefer pass by reference
  * using raw pointers for performance reasons can be discussed with the group after the code is operational

**C++ libraries:**
* Use `<iostream>` instead of `printf`
* Use `<string>` for strings
* Use `<vector>` or `<array>` for arrays
* Use `<thread>` for threads
* Use `<algorithm>` functions when applicable