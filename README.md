This is a smart Makefile that I have written and used over the years. It will look in your current directory and compile any .c or .cpp that it finds. It also builds dependencies from your c and cpp files (headers used) and will recompile your source file if one of the dependency has been modified. It uses a Makefile.spec file to modify the default behaviour (for example, compile a subdirectory, link as a library, etc...).

It also comes built-in with colorgcc.

Here's how you use it:

##What's Done Automatically
You will automatically get the following:
Your OS name as a global macro definition (-DYOUR_OS_NAME)
Dependency tracking for every source file maintained in a .dep directory, meaning that a source file will only be recompiled if a dependency or the source file itself has changed.
Errors and warnings in color: simply type make colorgcc to install and use colorgcc.
A debug (default) and release mode: type make MODE=release to compile in release mode (debugging symbols off and optimizations on)
At any time, type make explain to get a description of what's going in in the Makefile's head (how it interprets your Makefile.spec and what it sees in your current directory).
Getting Started
Download the file somewhere, and just type make. You should see:m

```
$shell>make

You should to write a Makefile.spec file containing your target
as well as any special configuration.  For instance:

INCLUDE += <any other include path>
CFLAGS += <any other compiler flag>
LDFLAGS += <any other linker flag>
LINKWITH += <list of library to link with>
EXECUTABLE = <name of the executable to create>
ARCHIVE = <name of the static library to create>
DYNLIB = <name of the dynamic library to create>
SUBDIR += <directory of dependency projects>

Object files are created in a "obj" directory and dependency
files are hidden in a ".dep" directory.  If you have problems
after adding or removing a source file, you should try deleting
the dependency file (or the whole dependency directory if you're
lazy).

If you want to know what's going to happen, try:
    make explain

If you'd like a default "Makefile.spec" to be conveniently
created for you, type
    make Makefile.spec

Finally, if you don't care about all this and just want to compile
your files into .o files, type:
    make objects
```
It's telling you that you need a Makefile.spec, and it's offering you to dump a template for you - which is what we'll do: make Makefile.spec.

```
$shell>make Makefile.spec

Makefile.spec file created
```

Open the Makefile.spec in your favorite text editor, you'll find this:

```
# Default Makefile.spec

#Additional include paths
INCLUDE +=

#Additional C flags
CFLAGS +=

#Additional C++ flags
CPPFLAGS +=

#List of libraries to link with
LINKWITH +=

#Additional Linker flags
LDFLAGS +=

#Name of the executable to create
EXECUTABLE =

#Name of the static library to create
ARCHIVE =

#Name of the dynamic library to create
DYNLIB =

#Other directories to update before compiling
SUBDIR =
```

For now, we'll just build an executable.

##Building an Executable
To build an executable, simply set a value to "EXECUTABLE" in your Makefile.spec. For example:

```
# Default Makefile.spec

#Additional include paths
INCLUDE +=

#Additional C flags
CFLAGS +=

#Additional C++ flags
CPPFLAGS +=

#List of libraries to link with
LINKWITH +=

#Additional Linker flags
LDFLAGS +=

#Name of the executable to create
EXECUTABLE = test

#Name of the static library to create
ARCHIVE =

#Name of the dynamic library to create
DYNLIB =

#Other directories to update before compiling
SUBDIR =
```

Whatever cpp and c files found in your directory will be compiled and linked together into an executable named (in this case) test. For example, we have a test.cpp file:

```
$shell>make

--------------------[test.d]-----------------
g++ -M -Wall -DDarwin -O3  -Weffc++   test.cpp | sed "s|test.o:|.dep/test.d:|" > .dep/test.d


=====================[test.o]=====================
g++ -Wall -DDarwin -O3  -Weffc++   -c -o obj/test.o ./test.cpp

##################[test]################
g++ -o test    obj/test.o   

```

```
$shell>./test 
hello world!!!
```


###Building a Static Library
Building a static library is just as simple as building an executable. In your Makefile.spec:

```
# Default Makefile.spec

#Additional include paths
INCLUDE +=

#Additional C flags
CFLAGS +=

#Additional C++ flags
CPPFLAGS +=

#List of libraries to link with
LINKWITH +=

#Additional Linker flags
LDFLAGS +=

#Name of the executable to create
EXECUTABLE = 

#Name of the static library to create
ARCHIVE = mylib

#Name of the dynamic library to create
DYNLIB =

#Other directories to update before compiling
SUBDIR =
```

When typing make, you'll get:

```
$shell>make

--------------------[test.d]-----------------
g++ -M -Wall -DDarwin -O3  -Weffc++   test.cpp | sed "s|test.o:|.dep/test.d:|" > .dep/test.d


=====================[test.o]=====================
g++ -Wall -DDarwin -O3  -Weffc++   -c -o obj/test.o ./test.cpp

##################[mylib.a]################
ar rc "mylib.a"    obj/test.o
ranlib "mylib.a"
``` 

##Getting Colors
By default, errors in your code will look like this when you compile:

```
$shell>make

=====================[test.o]=====================
colorgcc g++ -Wall -DDarwin -O3  -Weffc++   -c -o obj/test.o ./test.cpp
./test.cpp: In function 'int main(int, const char**)':
./test.cpp:5: error: 'printf' was not declared in this scope
make: *** [obj/test.o] Error 1
```

If you have colorgcc installed in your paths, this makefile will detect it and use it. Otherwise, simply type make colorgcc and colorgcc will be installed in your current directory. Thereafter, your errors show up in red and warnings in yellow.

###There's More
There's more but this is just meant to get you started using this Makefile. For the rest, look at the file itself and see what's going on. The file has been keep as simple as possible and shouldn't be to difficult to modify...

