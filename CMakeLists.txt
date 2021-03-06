cmake_minimum_required(VERSION 3.5)

# cmake -Dtest=ON to turn on test mode
option(test "Build all tests." OFF) 

project(libasync)

set(CMAKE_CXX_STANDARD 17)

if(UNIX)
	set(CMAKE_CXX_FLAGS "-std=c++17 -fPIC -Wno-reorder")
	SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb -Wno-reorder")
	SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O2 -Wall")
endif()

if(NOT WIN32)
	string(ASCII 27 Esc)
	set(ColourReset "${Esc}[m")
	set(ColourBold  "${Esc}[1m")
	set(Red         "${Esc}[31m")
	set(Green       "${Esc}[32m")
	set(Yellow      "${Esc}[33m")
	set(Blue        "${Esc}[34m")
	set(Magenta     "${Esc}[35m")
	set(Cyan        "${Esc}[36m")
	set(White       "${Esc}[37m")
	set(BoldRed     "${Esc}[1;31m")
	set(BoldGreen   "${Esc}[1;32m")
	set(BoldYellow  "${Esc}[1;33m")
	set(BoldBlue    "${Esc}[1;34m")
	set(BoldMagenta "${Esc}[1;35m")
	set(BoldCyan    "${Esc}[1;36m")
	set(BoldWhite   "${Esc}[1;37m")
endif()

function(message)
  list(GET ARGV 0 MessageType)
  if(MessageType STREQUAL FATAL_ERROR OR MessageType STREQUAL SEND_ERROR)
    list(REMOVE_AT ARGV 0)
    _message(${MessageType} "${BoldRed}${ARGV}${ColourReset}")
  elseif(MessageType STREQUAL WARNING)
    list(REMOVE_AT ARGV 0)
    _message(${MessageType} "${BoldYellow}${ARGV}${ColourReset}")
  elseif(MessageType STREQUAL AUTHOR_WARNING)
    list(REMOVE_AT ARGV 0)
    _message(${MessageType} "${BoldCyan}${ARGV}${ColourReset}")
  elseif(MessageType STREQUAL STATUS)
    list(REMOVE_AT ARGV 0)
    _message(${MessageType} "${Green}${ARGV}${ColourReset}")
  else()
    _message("${ARGV}")
  endif()
endfunction()

if(MSVC)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17")
	set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)  # gtest option on msvc
	set(CMAKE_CXX_FLAGS "/W4 /EHsc")
endif()

include_directories(./include)
include_directories(./src)
link_directories(./src)
add_subdirectory(./src)

if(test STREQUAL "ON")
		add_subdirectory(../googletest) 
		enable_testing()
		add_subdirectory(tests)
		message("${Green}-- In test mode, tests and samples are built.${ColourReset}")
else()
		message("${Green}-- In production-mode, googletest, tests and samples are excluded.${ColourReset}")
endif()
