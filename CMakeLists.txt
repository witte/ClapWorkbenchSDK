cmake_minimum_required(VERSION 3.25)

set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/_install CACHE STRING "")
set(CMAKE_PREFIX_PATH    ${CMAKE_INSTALL_PREFIX}      CACHE STRING "")

project(ClapWorkbenchSDK)

include(FetchContent)
set(FETCHCONTENT_QUIET OFF) # to show download progress


set(RTAUDIO_BUILD_STATIC_LIBS ON)
set(RTAUDIO_TARGETNAME_UNINSTALL "rtaudio-uninstall")
set(RTAUDIO_BUILD_TESTING OFF)
FetchContent_Declare(
    RtAudio
    GIT_REPOSITORY https://github.com/witte/rtaudio.git
    GIT_TAG        fix-FetchContent  # Version 6.0.1 with fix for FetchContent
    GIT_PROGRESS TRUE
)
FetchContent_MakeAvailable(RtAudio)


set(RTMIDI_BUILD_STATIC_LIBS ON)
set(RTMIDI_TARGETNAME_UNINSTALL "rtmidi-uninstall")
set(RTMIDI_BUILD_TESTING OFF)
FetchContent_Declare(
    RtMidi
    GIT_REPOSITORY https://github.com/thestk/rtmidi.git
    GIT_TAG        6.0.0
    GIT_PROGRESS TRUE
)
FetchContent_MakeAvailable(RtMidi)


FetchContent_Declare(
    clap
    GIT_REPOSITORY https://github.com/free-audio/clap.git
    GIT_TAG 1.2.1
    GIT_PROGRESS TRUE
)
FetchContent_MakeAvailable(clap)


FetchContent_Declare(
    clap-helpers
    GIT_REPOSITORY https://github.com/free-audio/clap-helpers.git
    GIT_TAG main
    GIT_PROGRESS TRUE
)
FetchContent_MakeAvailable(clap-helpers)


FetchContent_Declare(
   Qt6
   URL      https://download.qt.io/official_releases/qt/6.7/6.7.2/single/qt-everywhere-src-6.7.2.tar.xz
   URL_HASH MD5=06d35b47349c7c0a45710daad359e07b
)

# We try to remove as much as possible, but some of these can break compilation if set to OFF,
# and which ones could even change with different Qt versions
set(QT_BUILD_SUBMODULES "qtbase;qtshadertools;qtdeclarative")
set(BUILD_SHARED_LIBS        OFF)
set(QT_FEATURE_release       ON)
set(QT_FEATURE_optimize_full ON)

# set(FEATURE_ltcg          OFF)
#set(QT_FEATURE_settings      OFF)
set(QT_FEATURE_widgets       OFF)
# set(QT_FEATURE_network      OFF)
set(QT_FEATURE_printsupport OFF)
set(QT_FEATURE_sql          OFF)
set(QT_FEATURE_testlib      OFF)
set(QT_FEATURE_xml          OFF)

set(QT_FEATURE_texthtmlparser     ON)
set(QT_FEATURE_cssparser          OFF)
set(QT_FEATURE_textodfwriter      OFF)
set(QT_FEATURE_textmarkdownreader OFF)
set(QT_FEATURE_textmarkdownwriter OFF)

set(QT_FEATURE_doubleconversion ON)
set(QT_FEATURE_androiddeployqt  OFF)
set(QT_FEATURE_dbus             OFF)
set(QT_DEBUG_FIND_PACKAGE       ON)

FetchContent_MakeAvailable(Qt6)


# We need to build the basic tools (mainly `moc`) first or else Qt's modules compilation fails
if (NOT EXISTS "${CMAKE_BINARY_DIR}/_deps/qt6-build/libexec/moc")
    execute_process(COMMAND ${CMAKE_COMMAND} --build "${CMAKE_BINARY_DIR}/_deps/qt6-build" --parallel)
endif()

# this is so the Qt modules can find and use the basic tools
list(APPEND CMAKE_PREFIX_PATH "${CMAKE_BINARY_DIR}/_deps/qt6-build/lib/cmake/")

