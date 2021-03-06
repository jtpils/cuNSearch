cmake_minimum_required(VERSION 3.12)
#Requires cmake 3.12 for first class cuda support with visual studio

project(cuNSearch LANGUAGES CXX CUDA)
# Visual studio solution directories.
set_property(GLOBAL PROPERTY USE_FOLDERS on)


option(CUNSEARCH_USE_DOUBLE_PRECISION "Use double precision." ON)

if(CUNSEARCH_USE_DOUBLE_PRECISION)
	message(STATUS "cuNSearch::Real = double")
else()
	message(STATUS "cuNSearch::Real = float")
endif(CUNSEARCH_USE_DOUBLE_PRECISION)

if(CUNSEARCH_USE_DOUBLE_PRECISION)
	add_compile_options(-DCUNSEARCH_USE_DOUBLE_PRECISION)
endif(CUNSEARCH_USE_DOUBLE_PRECISION)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
SET(CMAKE_DEBUG_POSTFIX "_d")

find_package(CUDA 9.0 REQUIRED)

set (INCLUDE_HEADERS
		include/PointSet.h
		include/ActivationTable.h
		include/Common.h
        include/cuNSearch.h
         )

set (HEADER_FILES
		src/Types.h
        src/cuNSearchDeviceData.h
		src/GridInfo.h
		src/NotImplementedException.h
		src/PointSetImplementation.h
		src/cuNSearchKernels.cuh
		src/helper_linearIndex.h
     	src/helper_mortonCode.h
		Utils/cuda_helper.h
		Utils/Timing.h
		Utils/IDFactory.h
         )

set (SOURCE_FILES
		src/PointSet.cpp
		src/PointSetImplementation.cu
        src/cuNSearch.cu
        src/cuNSearchDeviceData.cu
		src/cuNSearchKernels.cu
		Utils/cuda_helper.cpp
		Utils/Timing.cpp
		Utils/IDFactory.cpp
		)

include_directories(
	"include"
	"Utils"
	${CUDA_INCLUDE_DIRS}
	${CUDA_CUT_INCLUDE_DIR}
        )


add_library(cuNSearch STATIC ${INCLUDE_HEADERS} ${HEADER_FILES} ${SOURCE_FILES})

target_link_libraries(cuNSearch ${CUDA_LIBRARIES})
target_compile_definitions(cuNSearch PUBLIC $<$<CONFIG:DEBUG>:DEBUG>)

install(FILES ${INCLUDE_HEADERS}
	DESTINATION include/)

install(TARGETS cuNSearch
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
	)

option(BUILD_DEMO "Build example of how to use this library."
		ON)
if(BUILD_DEMO)
	add_subdirectory(demo)
endif(BUILD_DEMO)


unset(USE_DOUBLE_PRECISION CACHE)
