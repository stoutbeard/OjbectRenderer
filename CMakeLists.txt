project(ObjectRenderer)
cmake_minimum_required(VERSION 3.2)
set(CMAKE_VERBOSE_MAKEFILE TRUE)
set(CMAKE_MODULE_PATH
	"${ObjectRenderer_SOURCE_DIR}"
	"${ObjectRenderer_SOURCE_DIR}/cmake"
	"${ObjectRenderer_SOURCE_DIR}/cmake/Packages"
)

SET(ObjectRenderer_INCLUDE_DIR "${ObjectRenderer_SOURCE_DIR}/include")
include_directories("${ObjectRenderer_INCLUDE_DIR}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")

# Find dependencies
include(Dependencies)

set(OBJECTRENDERER_SRCS
	"src/main.cpp"
	"src/Renderer.cpp"
	)
find_package(OpenCL REQUIRED)

if (NOT Boost_FOUND)
	MESSAGE(FATAL_ERROR "--> Could not locate required Boost files - Please check ${BOOST_SEARCH_PATH}")
endif()

if (NOT OPENIMAGEIO_FOUND)
	MESSAGE(FATAL_ERROR "--> Could not locate required OpenImageIO files - Please check ${OPENIMAGEIO_SEARCH_PATH}")
endif()

if (NOT OPENCL_FOUND)
	MESSAGE(ERROR "--> Could not locate OpenCL. Try one of the follwing:") 
	MESSAGE( "export ATISTREAMSDKROOT=/path/to/AMDAPPSDK-3.0")
	MESSAGE( "export ATISTREAMSDKROOT=/home/yourname/AMDAPPSDK-3.0")
	MESSAGE( "export AMDAPPSDKROOT=/path/to/AMDAPPSDKROOT")
	MESSAGE( "export CUDA_PATH=/path/to/NVIDIA/CUDA-7.0")
	MESSAGE( "export INTELOCLSDKROOT=/path/to/intel_ocl_sdk_root")
	SET(LUXRAYS_DISABLE_OPENCL 1)
endif()

if (NOT OPENGL_FOUND)
	MESSAGE(ERROR "--> Could not locate required OpenGL files, disabling OpenCL support, disabling samples build")
endif()

if (NOT GLEW_FOUND)
	MESSAGE(FATAL_ERROR "--> Could not locate required GLEW files, disabling samples build - Please check ${GLEW_SEARCH_PATH}")
endif()

if (NOT EMBREE_FOUND)
	MESSAGE(FATAL_ERROR "--> Could not locate required Intel Embree files - Please check ${EMBREE_SEARCH_PATH}")
endif()

ADD_DEFINITIONS(-msse2 -mfpmath=sse -ftree-vectorize -funroll-loops -Wall -fPIC -DHAVE_PTHREAD_H)

if (OPENCL_FOUND)
	include_directories(BEFORE SYSTEM ${OPENCL_INCLUDE_DIR} ${OPENCL_C_INCLUDE_DIR})
endif()


link_directories("${ObjectRenderer_SOURCE_DIR}/lib")
include_directories("include")

add_executable(objectrenderer ${OBJECTRENDERER_SRCS})
add_definitions(${VISIBILITY_FLAGS})

TARGET_LINK_LIBRARIES(
	objectrenderer 
	luxcore 
	smallluxgpu 
	luxrays
	${OPENEXR_LIBRARIES} 
	${EMBREE_LIBRARY} 
	${Boost_LIBRARIES} 
	${OPENGL_gl_LIBRARY} 
	${OPENIMAGEIO_LIBRARIES} 
	${OPENCL_LIBRARIES}  
	)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR})
message(${CMAKE_SOURCE_DIR})
message(${PROJECT_BINARY_DIR})
