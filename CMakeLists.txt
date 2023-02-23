cmake_minimum_required(VERSION 3.0 FATAL_ERROR)
project(torch_cpp CXX)

set(TORCH_URL https://download.pytorch.org/libtorch/cpu/libtorch-cxx11-abi-shared-with-deps-1.6.0%2Bcpu.zip)
set(TORCH_URL_MD5 21c5ef08e13aaeb16ec7c2eb1c0e75e9)

include(ExternalProject)
ExternalProject_Add(torch_archive
  URL               ${TORCH_URL}
  URL_MD5           ${TORCH_URL_MD5}
  PREFIX            torch_archive
  CONFIGURE_COMMAND ""
  BUILD_COMMAND     ""
  INSTALL_COMMAND   ""
)

ExternalProject_Get_Property(torch_archive SOURCE_DIR)

set(TORCH_PATH ${SOURCE_DIR})


add_library(${PROJECT_NAME} SHARED IMPORTED)
add_dependencies(${PROJECT_NAME} torch_ext_archive)

enable_testing()
add_executable(${PROJECT_NAME}-test
    test/dummy_test.cc
    )
target_link_libraries(${PROJECT_NAME}-test
    PRIVATE
    ${TORCH_LIBRARIES}
    )
add_test(${PROJECT_NAME}-test ${PROJECT_NAME}-test)

# manually install header, libraries and config files

include(GNUInstallDirs)

install(DIRECTORY ${TORCH_PATH}/include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    FILES_MATCHING PATTERN "*.h*"
)

install(DIRECTORY ${TORCH_PATH}/lib/
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(DIRECTORY ${TORCH_PATH}/share/
    DESTINATION ${CMAKE_INSTALL_DATADIR}
)
