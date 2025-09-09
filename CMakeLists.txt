set(PROJECT_NAME "obs_scene_tree_view")
set(HEADER_DIRECTORY "obs_scene_tree_view")

set(NAMESPACE_NAME "${PROJECT_NAME}")

set(LIBRARY_NAME "${PROJECT_NAME}")
set(EXECUTABLE_NAME "${PROJECT_NAME}Exec")
set(TEST_NAME "${PROJECT_NAME}Tests")

set(LIB_EXPORT_NAME "${LIBRARY_NAME}Targets")
set(LIB_CONFIG_NAME "${LIBRARY_NAME}Config")
set(LIB_VERSION_NAME "${LIB_CONFIG_NAME}Version")

cmake_minimum_required(VERSION 3.28...3.30)
project("${PROJECT_NAME}" VERSION 0.1.5)


find_package(Qt6 REQUIRED Widgets)

if(OS_LINUX OR OS_FREEBSD OR OS_OPENBSD)
  find_package(Qt6 REQUIRED Gui)

  find_package(X11 REQUIRED)
endif()

set(LIB_SRC_FILES
		obs_scene_tree_view/obs_scene_tree_view.cpp
		obs_scene_tree_view/stv_item_model.cpp
		obs_scene_tree_view/stv_item_view.cpp
)


##########################################
## Version
configure_file(cmake/version.h.in "${CMAKE_CURRENT_BINARY_DIR}/include/${HEADER_DIRECTORY}/version.h" @ONLY)


##########################################
## Qt6 UI
set(CMAKE_AUTOUIC_SEARCH_PATHS ${CMAKE_AUTOUIC_SEARCH_PATHS} "${CMAKE_CURRENT_SOURCE_DIR}/forms")
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)


##########################################
## Library
add_library(${LIBRARY_NAME} SHARED ${LIB_SRC_FILES} ${VT_UI_HEADERS})
add_library("${NAMESPACE_NAME}::${LIBRARY_NAME}" ALIAS ${LIBRARY_NAME})

set_target_properties(${LIBRARY_NAME} PROPERTIES PREFIX "")

target_include_directories(${LIBRARY_NAME}
		PRIVATE
				"$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
				"$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>"
				"$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
)

target_compile_options(${LIBRARY_NAME} PRIVATE $<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:GNU>>:-Wall -Wextra>)

target_link_libraries(
  ${LIBRARY_NAME}
  PRIVATE
    OBS::libobs
    OBS::frontend-api
    Qt::Widgets
)


set_target_properties_obs(
  ${LIBRARY_NAME}
  PROPERTIES FOLDER plugins/${LIBRARY_NAME}
             PREFIX ""
             AUTOMOC ON
             AUTOUIC ON
             AUTORCC ON
             AUTOUIC_SEARCH_PATHS forms
)
