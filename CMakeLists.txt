#
# Copyright Soramitsu Co., Ltd. All Rights Reserved.
# SPDX-License-Identifier: Apache-2.0
#

cmake_minimum_required(VERSION 3.12)

include("cmake/HunterGate.cmake")
HunterGate(
  URL "https://github.com/cpp-pm/hunter/archive/v0.23.216.tar.gz"
  SHA1 "a492a1879bee60c29f7af17ef56b45f0d7766241"
  LOCAL
)

project("SQLiteModernCpp"
  LANGUAGES CXX
  VERSION 1.0.0
  )

hunter_add_package(sqlite3)
find_package(sqlite3 CONFIG REQUIRED)

add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(
  ${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:"${CMAKE_CURRENT_SOURCE_DIR}/hdr">
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDE_DIR}>
)
target_link_libraries(${PROJECT_NAME} INTERFACE sqlite3::sqlite3)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)

include(GNUInstallDirs)

function(install_target targets config)
  install(TARGETS ${targets} EXPORT ${config}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    FRAMEWORK DESTINATION ${CMAKE_INSTALL_PREFIX}
    )
endfunction()

set(CONFIG_NAME "${PROJECT_NAME}Config")
install_target(${PROJECT_NAME} ${CONFIG_NAME})

install(
  DIRECTORY ${CMAKE_SOURCE_DIR}/hdr/
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

set(CONFIG_DESTINATION_DIR "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
install(
  EXPORT ${CONFIG_NAME}
  NAMESPACE SQLiteModernCpp::
  DESTINATION ${CONFIG_DESTINATION_DIR}
)
