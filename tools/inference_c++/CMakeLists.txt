cmake_minimum_required(VERSION 3.0)
project(Main)

if(NOT DEFINED PADDLE_LIB)
  message(FATAL_ERROR "please set PADDLE_LIB with -DPADDLE_LIB=/path/paddle/lib")
endif()

include_directories("${PADDLE_LIB}/")
include_directories("${PADDLE_LIB}/third_party/install/protobuf/include")
include_directories("${PADDLE_LIB}/third_party/install/glog/include")
include_directories("${PADDLE_LIB}/third_party/install/gflags/include")
include_directories("${PADDLE_LIB}/third_party/install/xxhash/include")

link_directories("${PADDLE_LIB}/paddle/lib")
link_directories("${PADDLE_LIB}/third_party/install/protobuf/lib")
link_directories("${PADDLE_LIB}/third_party/install/glog/lib")
link_directories("${PADDLE_LIB}/third_party/install/gflags/lib")
link_directories("${PADDLE_LIB}/third_party/install/xxhash/lib")

add_executable(${INFER_NAME} ${INFER_NAME}.cpp)
if(WITH_MKL)
    include_directories("${PADDLE_LIB}/third_party/install/mklml/include")
    include_directories("${PADDLE_LIB}/third_party/install/mkldnn/include")
    set(MATH_LIB ${PADDLE_LIB}/third_party/install/mklml/lib/libmklml_intel.so ${PADDLE_LIB}/third_party/install/mklml/lib/libiomp5.so ${PADDLE_LIB}/third_party/install/mkldnn/lib/libdnnl.so.1)
endif()
if(WITH_GPU)
    set(CUDA_LIB "/usr/local/cuda/lib64/" CACHE STRING "CUDA Library")
    set(DEPS ${CUDA_LIB}/libcudart.so)
endif()

set(DEPS ${DEPS} ${PADDLE_LIB}/paddle/lib/libpaddle_inference.so)
set(EXTERNAL_LIB "-lrt -ldl -lpthread")
set(DEPS ${DEPS} ${MATH_LIB}  glog gflags protobuf xxhash ${EXTERNAL_LIB})
target_link_libraries(${INFER_NAME} ${DEPS})
