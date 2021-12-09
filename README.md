# conemsgui
A template project of imgui + sdl that can compile for the web. Using cmake, conan (optional), and emscripten. Can compile to WebAssembly (WASM).
## Instructions for emscripten:
1. Install dependencies using conan:
```
conan install . -if=build-emscripten -pr:h=emscripten.profile -pr:b=default --build=missing
```
2. activate the environment (sets up the necessary emsdk environment variables):
```
source build-emscripten/activate.sh
```
3. build normally using cmake:
```
cmake -B build-emscripten -S .
cmake --build build-emscripten/ -- -j 4
```
## Instructions for native builds:
1. Install dependencies using conan (you might need to install some system libs like libglu1-mesa-dev).
```
conan install . -if=build-native --build=missing
```
2. build normally using cmake:
```
cmake -B build-native -S .
cmake --build build-native/ -- -j 4
```
