cmake_minimum_required(VERSION 3.12...3.14 FATAL_ERROR)
project(GTC)

#options
option(GTC_ENABLE_CUDA "Enable CUDA" OFF)

if(GTC_ENABLE_CUDA)
  enable_language(CUDA)
endif()

add_library(gtc_compiler_flags INTERFACE)
target_compile_features(gtc_compiler_flags
                        INTERFACE cxx_std_11)
set(CMAKE_CXX_EXTENSIONS Off)


add_library(gtc_lib STATIC)
target_sources(gtc_lib PRIVATE serial.cxx)
if(GTC_ENABLE_CUDA)
  target_sources(gtc_lib PRIVATE parallel.cu)
endif()

target_link_libraries(gtc_lib PUBLIC gtc_compiler_flags)
target_include_directories(gtc_lib
  PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}
  INTERFACE $<INSTALL_INTERFACE:include/gtc>)
set_target_properties(gtc_lib PROPERTIES CUDA_SEPARABLE_COMPILATION ON)


add_executable(gtc)
target_sources(gtc PRIVATE main.cxx)
target_link_libraries(gtc PRIVATE gtc_lib)


#/usr/bin/c++   -I/presentations/S9444  -std=c++11 -o CMakeFiles/gtc_lib.dir/serial.cxx.o -c /presentations/S9444/serial.cxx
#/usr/local/cuda/bin/nvcc   -I/presentations/S9444  -std=c++11 -x cu -c /presentations/S9444/parallel.cu -o CMakeFiles/gtc_lib.dir/parallel.cu.o
#/usr/bin/c++ -std=c++11 -o CMakeFiles/gtc.dir/main.cxx.o -c /presentations/S9444/main.cxx'
#/usr/bin/c++ CMakeFiles/gtc.dir/main.cxx.o -o gtc  -L/usr/local/cuda/lib64/stubs  -L/usr/local/cuda/lib64 libgtc_lib.a -lcudadevrt -lcudart_static -lrt -lpthread -ldl


# with separable
#/usr/local/cuda/bin/nvcc   -I/presentations/S9444  -std=c++11 -x cu -dc /presentations/S9444/parallel.cu -o CMakeFiles/gtc_lib.dir/parallel.cu.o
# /usr/local/cuda/bin/nvcc   -Xcompiler=-fPIC -Wno-deprecated-gpu-targets -shared -dlink CMakeFiles/gtc.dir/main.cxx.o -o CMakeFiles/gtc.dir/cmake_device_link.o  -L/usr/local/cuda/lib64/stubs  -L/usr/local/cuda/lib64 libgtc_lib.a -lcudadevrt -lcudart_static -lrt -lpthread -ldl
#/usr/bin/c++ CMakeFiles/gtc.dir/main.cxx.o CMakeFiles/gtc.dir/cmake_device_link.o -o gtc  -L/usr/local/cuda/lib64/stubs  -L/usr/local/cuda/lib64 libgtc_lib.a -lcudadevrt -lcudart_static -lrt -lpthread -ldl
