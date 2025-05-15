This repo is a simple template for fairly modern opengl application
Allows use of GLEW, GLFW3 and GLU

# Make out of source build files
mkdir ../../build/glew_glfw_template
cmake -S . -B ../../build/glew_glfw_template

# Build project
cd ../../build/glew_glfw_template
cmake --build .

# Run project
cd Debug
Example.exe

