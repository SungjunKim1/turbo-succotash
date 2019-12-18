
cmake_minimum_required(VERSION 3.10)
project(succotash LANGUAGES CXX)

add_library(socket
  socket.h
  socket.cpp
)

set_target_properties(socket
PROPERTIES
  CXX_STANDARD 14
  CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE
)

target_include_directories(socket
PUBLIC
  ${PROJECT_SOURCE_DIR}
)

if(WIN32)
  target_link_libraries(socket
  PUBLIC
    ${CMAKE_DL_LIBS} ws2_32
  )
else()
  target_link_libraries(socket
  PUBLIC
    ${CMAKE_DL_LIBS}
  )
endif()

target_compile_definitions(socket
PUBLIC
    MAINTAINER="dev@mail.com"
)

if(${CMAKE_CXX_COMPILER_ID} MATCHES Clang) # Apple'Clang'
  target_compile_options(socket
  PUBLIC
    -Wall -Wextra
    -stdlib=libc++ 
  )
elseif(${CMAKE_CXX_COMPILER_ID} MATCHES GNU) # GCC
  target_compile_options(socket
  PUBLIC
    -Wall
  )
elseif(MSVC)
  target_compile_options(socket
  PUBLIC
    /W4
  )
endif()
