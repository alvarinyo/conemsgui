find_package(fmt REQUIRED)
find_package(SDL2 REQUIRED)
find_package(imgui REQUIRED)

# Library for the imgui sdl-opengl3 bindings
add_library(
  imgui_bindings
  ${imgui_RES_DIRS}/bindings/imgui_impl_sdl.h
  ${imgui_RES_DIRS}/bindings/imgui_impl_sdl.cpp
  ${imgui_RES_DIRS}/bindings/imgui_impl_opengl3.h
  ${imgui_RES_DIRS}/bindings/imgui_impl_opengl3.cpp
)

target_link_libraries(
  imgui_bindings
  PUBLIC
  imgui::imgui
  SDL2::SDL2
)

# Project executable
add_executable(
  ${PROJECT_NAME} 
  main.cpp
)

target_include_directories(
  ${PROJECT_NAME}
  PUBLIC
  ${imgui_RES_DIRS}/bindings
  ${SDL2_INCLUDE_DIRS}
)

target_link_libraries(
  ${PROJECT_NAME} 
  PRIVATE 
  project_options
  project_warnings
  imgui_bindings
  PUBLIC
  fmt::fmt 
)

set_target_properties(${PROJECT_NAME} PROPERTIES
  SUFFIX ".html"
  LINK_FLAGS "--emrun"
)

file(COPY index.html
  DESTINATION "${CMAKE_CURRENT_BINARY_DIR}"
)