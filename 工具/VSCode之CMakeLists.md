```cmake
cmake_minimum_required(VERSION 3.0.0)
project(filter VERSION 0.1.0)


set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g -Wall -Wextra")
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Wall -Wextra")
aux_source_directory(./ SRC)
add_executable(filter ${SRC})

```

