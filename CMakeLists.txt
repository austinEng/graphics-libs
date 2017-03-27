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
  CONFIGURE_COMMAND ${CMAKE_COMMAND} ../openexr/IlmBase -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Get_Property(openexr source_dir)
ExternalProject_Get_Property(openexr binary_dir)

add_custom_target(openexr_main_directory ALL COMMAND ${CMAKE_COMMAND} -E make_directory ${binary_dir}/../openexr_main-build)
add_custom_target(openexr_viewers_directory ALL COMMAND ${CMAKE_COMMAND} -E make_directory ${binary_dir}/../openexr_viewers-build)

ExternalProject_Add(openexr_main
  SOURCE_DIR ${source_dir}/OpenEXR
  BINARY_DIR ${binary_dir}/../openexr_main-build
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR} -DILMBASE_PACKAGE_PREFIX=${PROJECT_SOURCE_DIR}
)

ExternalProject_Add(openexr_viewers
  SOURCE_DIR ${source_dir}/OpenEXR_Viewers
  BINARY_DIR ${binary_dir}/../openexr_viewers-build
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR} -DOPENEXR_PACKAGE_PREFIX=${PROJECT_SOURCE_DIR}
)

add_dependencies(openexr_main openexr_main_directory openexr)
add_dependencies(openexr_viewers openexr_viewers_directory openexr_main)

ExternalProject_Add(tbb
  GIT_REPOSITORY git@github.com:wjakob/tbb.git
  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
)

if (AE_CORE_DIR)
  ExternalProject_Add(ae_core
    SOURCE_DIR ${AE_CORE_DIR}
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
  )
else()
  ExternalProject_Add(ae_core
    GIT_REPOSITORY git@github.com:austinEng/ae_core.git
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
  )
endif()

ExternalProject_Add(libpng
  GIT_REPOSITORY https://github.com/jherico/libpng.git
  CONFIGURE_COMMAND cmake ../libpng
  BUILD_COMMAND make
  INSTALL_COMMAND
    cp libpng.a ${PROJECT_SOURCE_DIR}/lib &&
    mkdir -p ${PROJECT_SOURCE_DIR}/include/png &&
    cp ./pnglibconf.h ${PROJECT_SOURCE_DIR}/include/png &&
    cp ../libpng/png.h ../libpng/pngdebug.h ../libpng/pnginfo.h ../libpng/pngpriv.h ../libpng/pngstruct.h ../libpng/pngconf.h ${PROJECT_SOURCE_DIR}/include/png
)

#ExternalProject_Add(GLEW
#  GIT_REPOSITORY git@github.com:nigels-com/glew.git
#  CONFIGURE_COMMAND cd ../GLEW/auto && make && cd ../ && cmake ../GLEW/build/cmake -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
#)

#ExternalProject_Add(GLFW
#  GIT_REPOSITORY git@github.com:glfw/glfw.git
#  CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${PROJECT_SOURCE_DIR}
#)
