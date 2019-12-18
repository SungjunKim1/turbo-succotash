
cmake_minimum_required(VERSION 3.10)
project(succotash VERSION 1.0
        LANGUAGES CXX)

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
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_PREFIX}/include>
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


install(FILES   socket.h
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include
)

install(TARGETS         socket
        EXPORT          socket-config
        RUNTIME   DESTINATION ${CMAKE_INSTALL_PREFIX}/bin/${TARGET}
        LIBRARY   DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/${TARGET}
        ARCHIVE   DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/${TARGET}
        INCLUDES  DESTINATION ${CMAKE_INSTALL_PREFIX}/include
)

install(EXPORT      socket-config
        DESTINATION ${CMAKE_INSTALL_PREFIX}/share/socket
)

include(CMakePackageConfigHelpers)
set(VERSION_FILE_PATH   ${CMAKE_BINARY_DIR}/cmake/socket-config-version.cmake)
write_basic_package_version_file(${VERSION_FILE_PATH}
    VERSION             ${PROJECT_VERSION}
    COMPATIBILITY       SameMajorVersion
)

install(FILES           ${VERSION_FILE_PATH} 
        DESTINATION     ${CMAKE_INSTALL_PREFIX}/share/socket
)
