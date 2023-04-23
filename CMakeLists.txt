cmake_minimum_required(VERSION 3.22)
project( "onevpl_sample" )
set( SOURCE_CODE "onevpl_sample" )

#if("${CMAKE_CXX_COMPILER}" STREQUAL "")
#    set(CMAKE_CC_COMPILER "icx")
#    set(CMAKE_CXX_COMPILER "icpx")
#    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsycl")
#endif()


# find pakages
find_package( OpenCV REQUIRED )
find_package( VPL REQUIRED )
find_package( GFLAGS REQUIRED )



include_directories( ${OpenCV_INCLUDE_DIRS} ${VPL_INCLUDE_DIRS})

add_executable( ${SOURCE_CODE} ${SOURCE_CODE}.cpp )
target_link_libraries( ${SOURCE_CODE} ${OpenCV_LIBS} ${VPL_LIBRARIES} ${GFLAGS} )


#file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/src DESTINATION . FILES_MATCHING PATTERN "*.*")
#file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/CMakeLists.txt DESTINATION .)
#file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/PreLoad.cmake DESTINATION .)
#add_subdirectory (${PROJECT_BINARY_DIR}/ bin)
