cmake_minimum_required(VERSION 3.20...3.23)

project(BuildNcurses LANGUAGES NONE)

include(ExternalProject)

set(ncurses_url https://invisible-mirror.net/archives/ncurses/current/ncurses.tar.gz)

# --- auto-ignore build directory
if(NOT EXISTS ${PROJECT_BINARY_DIR}/.gitignore)
  file(WRITE ${PROJECT_BINARY_DIR}/.gitignore "*")
endif()

# users can specify like "cmake -B build -DCMAKE_INSTALL_PREFIX=~/mydir"
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  message(FATAL_ERROR "please specify install prefix like
  cmake -B build -DCMAKE_INSTALL_PREFIX=~/mylibs"
  )
endif()

set(CMAKE_TLS_VERIFY true)

set_directory_properties(PROPERTIES EP_UPDATE_DISCONNECTED true)

find_program(MAKE_EXECUTABLE NAMES gmake make mingw32-make REQUIRED)

set(ncurses_args
--prefix=${CMAKE_INSTALL_PREFIX}
)
if(BUILD_SHARED_LIBS)
  list(APPEND ncurses_args --with-shared)
endif()

ExternalProject_Add(NCURSES
URL ${ncurses_url}
CONFIGURE_COMMAND <SOURCE_DIR>/configure ${ncurses_args}
BUILD_COMMAND ${MAKE_EXECUTABLE} -j
INSTALL_COMMAND ${MAKE_EXECUTABLE} -j install
TEST_COMMAND ""
INACTIVITY_TIMEOUT 15
CONFIGURE_HANDLED_BY_BUILD ON
)
