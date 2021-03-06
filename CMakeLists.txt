project(libCMT)

cmake_minimum_required(VERSION 2.6)
find_package(OpenCV REQUIRED)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

option(BUILD_MAIN "Build main" ON)
option(BUILD_WITH_QT "Use QDebug" OFF)

include_directories(${OpenCV_INCLUDE_DIRS})
link_directories(${OpenCV_LIB_DIR})

add_library(cmt SHARED CMT.cpp)
target_link_libraries(cmt ${OpenCV_LIBS})

if(BUILD_MAIN)
	add_executable(main main.cpp)
	if(BUILD_WITH_QT)
		find_package(Qt4 REQUIRED)

		include(${QT_USE_FILE})
		add_definitions(${QT_DEFINITIONS} -DDEBUG_MODE)

		target_link_libraries(main cmt ${OpenCV_LIBS} ${QT_LIBRARIES})
	else()
		target_link_libraries(main cmt ${OpenCV_LIBS})
	endif()
endif()

install(TARGETS cmt
  LIBRARY DESTINATION lib
  PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE GROUP_READ GROUP_WRITE GROUP_EXECUTE WORLD_READ WORLD_WRITE WORLD_EXECUTE)
install(FILES CMT.h DESTINATION include/libcmt)

configure_file(libcmt.pc.in libcmt.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libcmt.pc DESTINATION lib/pkgconfig)