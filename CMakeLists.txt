cmake_minimum_required(VERSION 3.7)
project(IN55Project)

set(CMAKE_CXX_STANDARD 11)

set(CMAKE_INSTALL_PREFIX build/)
set(CMAKE_PREFIX_PATH C:/Qt/Qt5.6.0.2/5.6/msvc2015)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CMAKE_VERBOSE_MAKEFILE ON)

set(APP_MODELS src/models)
set(APP_WIDGETS src/widgets)
set(APP_UTIL src/util)
set(APP_CONTROLS src/controls)

include_directories( ${APP_MODELS} )
include_directories( ${APP_WIDGETS} )
include_directories( ${APP_UTIL} )
include_directories( ${APP_CONTROLS} )

find_package(Qt5 COMPONENTS Widgets OpenGL Gui Core REQUIRED)

set(SOURCE_FILES src/main.cpp
        ${APP_MODELS}/stb_image.h
        ${APP_MODELS}/gem.h
        ${APP_MODELS}/gem.cpp
        ${APP_MODELS}/skyBox.h
        ${APP_MODELS}/skyBox.cpp
        ${APP_MODELS}/stb_image.h
        ${APP_MODELS}/skyBox.cpp
        ${APP_MODELS}/skyBox.h
        ${APP_WIDGETS}/displayWidget.h
        ${APP_WIDGETS}/displayWidget.cpp
        ${APP_WIDGETS}/menuWidget.h
        ${APP_WIDGETS}/menuWidget.cpp
        ${APP_UTIL}/vertex.h
        ${APP_UTIL}/transform3D.h
        ${APP_UTIL}/transform3D.cpp
        ${APP_CONTROLS}/input.cpp
        ${APP_CONTROLS}/camera3D.h
        ${APP_CONTROLS}/camera3D.cpp
        ${APP_CONTROLS}/input.h)

add_executable(${PROJECT_NAME} ${SOURCE_FILES})

get_target_property(QtCore_location Qt5::Core LOCATION)
target_link_libraries(IN55Project Qt5::Widgets Qt5::OpenGL Qt5::Gui)

# create a list of files to copy
set( THIRD_PARTY_DLLS
   $<TARGET_FILE:Qt5::Widgets>
   $<TARGET_FILE:Qt5::Core>
   $<TARGET_FILE:Qt5::Gui>
)

if(WIN32)
    if($<CONFIG:Debug>)
        get_target_property(WIDGETDLL Qt5::Widgets IMPORTED_LOCATION_DEBUG)
    else($<CONFIG:Release>)
        get_target_property(WIDGETDLL Qt5::Widgets IMPORTED_LOCATION_RELEASE)
    endif($<CONFIG:Debug>)
    # do the copying
	foreach( file_i ${THIRD_PARTY_DLLS})
	    add_custom_command(
	    	TARGET ${PROJECT_NAME} POST_BUILD
		    COMMAND ${CMAKE_COMMAND} -E copy_if_different
		        ${file_i}
		        $<TARGET_FILE_DIR:${PROJECT_NAME}>
		)
	endforeach( file_i )

    #Copy the resources
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
                    COMMAND ${CMAKE_COMMAND} -E copy_directory
                    ${CMAKE_SOURCE_DIR}/resources $<TARGET_FILE_DIR:${PROJECT_NAME}>/resources)
endif(WIN32)
