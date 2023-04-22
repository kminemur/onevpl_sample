set(SOURCE_CODE onevpl_sample CACHE NAME "onevpl_sample")

# CMakeの最低バージョンを記述
cmake_minimum_required(VERSION 3.22)

if("${CMAKE_CXX_COMPILER}" STREQUAL "")
    set(CMAKE_CC_COMPILER "icx")
    set(CMAKE_CXX_COMPILER "icpx")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsycl")
endif()


# ソリューション名を指定
project( ${SOURCE_CODE} )

# OpenCVのパッケージを探す
find_package( OpenCV REQUIRED )

#ヘッダファイルのパスを指定
include_directories( ${OpenCV_INCLUDE_DIRS} )

# 実行ファイル名とソース指定（ここではソースと同じ名前の実行ファイルを作ります）
add_executable( ${SOURCE_CODE} ${SOURCE_CODE}.cpp )

#リンク先のライブラリを指定
target_link_libraries( ${SOURCE_CODE} ${OpenCV_LIBS} )


file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/src DESTINATION . FILES_MATCHING PATTERN "*.*")
file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/CMakeLists.txt DESTINATION .)
file(COPY $ENV{ONEAPI_ROOT}/vpl/latest/examples/hello/hello-decode/PreLoad.cmake DESTINATION .)
