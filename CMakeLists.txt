cmake_policy(SET CMP0048 OLD)
project(graphics_libs)
cmake_minimum_required(VERSION 2.8.6)

set(${PROJECT_NAME}_VERSION_MAJOR 0 CACHE STRING "major version" FORCE)
set(${PROJECT_NAME}_VERSION_MINOR 1 CACHE STRING "minor version" FORCE)
set(${PROJECT_NAME}_VERSION ${${PROJECT_NAME}_VERSION_MAJOR}.${${PROJECT_NAME}_VERSION_MINOR} CACHE STRING "version" FORCE)

include(ExternalProject)

ExternalProject_Add(glm
  GIT_REPOSITORY git@github.com:g-truc/glm.git
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(tinyobjloader
  GIT_REPOSITORY git@github.com:syoyo/tinyobjloader.git
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(openexr
  GIT_REPOSITORY git@github.com:openexr/openexr.git
  CONFIGURE_COMMAND cmake ../openexr/IlmBase -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(OpenEXR
  SOURCE_DIR openexr-prefix/src/openexr/OpenEXR
  CMAKE_ARGS 
    -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
    -DILMBASE_PACKAGE_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(tbb
  GIT_REPOSITORY git@github.com:wjakob/tbb.git
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(ae_core
  GIT_REPOSITORY git@github.com:austinEng/ae_core.git
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

add_dependencies(OpenEXR openexr)