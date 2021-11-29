if(CMAKE_CXX_COMPILER MATCHES "/em\\+\\+(-[a-zA-Z0-9.])?(\.bat)?$")
  set(USING_EMSCRIPTEN TRUE)
endif()


find_package(SDL2 REQUIRED)
find_package(imgui REQUIRED)

# Library for the imgui sdl-opengl3 bindings
add_library(
  imgui_bindings
  ${imgui_RES_DIRS}/bindings/imgui_impl_sdl.cpp
  ${imgui_RES_DIRS}/bindings/imgui_impl_sdl.h
  ${imgui_RES_DIRS}/bindings/imgui_impl_opengl3.cpp
  ${imgui_RES_DIRS}/bindings/imgui_impl_opengl3.h
)

target_link_libraries(
  imgui_bindings
  PUBLIC
  imgui::imgui
  SDL2::SDL2
)


find_package(fmt REQUIRED)
if (USING_EMSCRIPTEN)
  message("Emscripten detected. No opengl loader needed")
else()
  message("Using GLEW as opengl loader")
  find_package(GLEW REQUIRED)
  set(OPENGL_LOADER_LIBS GLEW::GLEW)
  set(OPENGL_LOADER_INCLUDES ${GLEW_INCLUDE_DIRS})
  set(OPENGL_LOADER_LIBS GLEW::GLEW)
  set(OPENGL_LOADER_COMPILE_DEFINITIONS IMGUI_IMPL_OPENGL_LOADER_GLEW)
endif()

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
  ${OPENGL_LOADER_INCLUDES}
)

target_link_libraries(
  ${PROJECT_NAME}
  PRIVATE 
  project_options
  project_warnings
  imgui_bindings
  PUBLIC
  fmt::fmt
  ${OPENGL_LOADER_LIBS}
)

target_compile_definitions(
  ${PROJECT_NAME} 
  PRIVATE 
  ${OPENGL_LOADER_COMPILE_DEFINITIONS}
)

if(USING_EMSCRIPTEN)
  message("Emscripten detected. Will generate ${PROJECT_NAME}.html and index.html files.")
  set_target_properties(${PROJECT_NAME} PROPERTIES
    SUFFIX ".html"
    LINK_FLAGS "--emrun"
  )
  file(COPY index.html
    DESTINATION "${CMAKE_CURRENT_BINARY_DIR}"
  )
endif()