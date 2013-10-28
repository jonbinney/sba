cmake_minimum_required(VERSION 2.8.3)
project(sba)

find_package(catkin REQUIRED)
find_package(Eigen REQUIRED)

include_directories(${catkin_INCLUDE_DIRS} ${EIGEN_INCLUDE_DIRS})
add_definitions(${EIGEN_DEFINITIONS})

INCLUDE_DIRECTORIES(include)

# f2c is sometimes equivalent to libF77 & libI77; in that case, set HAVE_F2C to 0
SET(HAVE_F2C 1 CACHE BOOL "Do we have f2c or F77/I77?" )

# the directory where the lapack/blas/f2c libraries reside
SET(LAPACKBLAS_DIR /usr/lib CACHE PATH "Path to lapack/blas libraries")

# actual names for the lapack/blas/f2c libraries
SET(LAPACK_LIB lapack CACHE STRING "The name of the lapack library")
SET(BLAS_LIB blas CACHE STRING "The name of the blas library")
IF(HAVE_F2C)
  SET(F2C_LIB f2c CACHE STRING "The name of the f2c library")
ELSE(HAVE_F2C)
  SET(F77_LIB libF77 CACHE STRING "The name of the F77 library")
  SET(I77_LIB libI77 CACHE STRING "The name of the I77 library")
ENDIF(HAVE_F2C)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES sba
)

add_definitions(-DSBA_CHOLMOD)

# SBA library
add_library(sba src/sba.cpp src/spa.cpp src/spa2d.cpp src/csparse.cpp src/proj.cpp src/node.cpp src/sba_file_io.cpp)
#rosbuild_add_compile_flags(sba ${SSE_FLAGS})
target_link_libraries(sba blas lapack cholmod cxsparse)

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY include/bpcg
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})