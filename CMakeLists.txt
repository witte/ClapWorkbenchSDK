cmake_minimum_required(VERSION 4.0)

set(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/__deps CACHE STRING "")
set(CMAKE_PREFIX_PATH    ${CMAKE_INSTALL_PREFIX}    CACHE STRING "")

# TODO: setup version from git tag or branch name + date
project(ClapWorkbenchSDK VERSION 0.0.2)


set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_OSX_DEPLOYMENT_TARGET 11.0)


include(FetchContent)
set(FETCHCONTENT_QUIET OFF) # to show download progress


set(RTAUDIO_BUILD_STATIC_LIBS ON)
set(RTAUDIO_TARGETNAME_UNINSTALL "rtaudio-uninstall")
set(RTAUDIO_BUILD_TESTING OFF)
FetchContent_Declare(
    RtAudio
    GIT_REPOSITORY  https://github.com/thestk/rtaudio.git
    GIT_TAG         409636b5dcad3054ae5a9e85014bba3861b8edab # Nov 25, 2025
    GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(RtAudio)


set(RTMIDI_BUILD_STATIC_LIBS ON)
set(RTMIDI_TARGETNAME_UNINSTALL "rtmidi-uninstall")
set(RTMIDI_BUILD_TESTING OFF)
FetchContent_Declare(
    RtMidi
    GIT_REPOSITORY  https://github.com/thestk/rtmidi.git
    GIT_TAG         a3233c22949342f6697681e2cf2403e27fcf0c9e # Nov 27, 2025
    GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(RtMidi)


FetchContent_Declare(
    clap
    GIT_REPOSITORY  https://github.com/free-audio/clap.git
    GIT_TAG         1.2.7
    GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(clap)


FetchContent_Declare(
    clap-helpers
    GIT_REPOSITORY  https://github.com/free-audio/clap-helpers.git
    GIT_TAG         a61bcdf0ecc2c8db1e80bfe8bf9cb7e8d9fd2bbc # Nov 28, 2025
    GIT_PROGRESS    TRUE
)
FetchContent_MakeAvailable(clap-helpers)


# We try to remove as much as possible, but some of these can break compilation if set to OFF,
# and the breaking ones could even change with different Qt versions
set(QT_BUILD_SUBMODULES "qtbase;qtshadertools;qtdeclarative")
set(BUILD_SHARED_LIBS        OFF)
set(QT_FEATURE_release       ON)
set(QT_FEATURE_optimize_full ON)

# set(FEATURE_ltcg            OFF)
# set(QT_FEATURE_settings     OFF)
set(QT_FEATURE_widgets      OFF)
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
FetchContent_Declare(
    Qt6
    URL      https://download.qt.io/official_releases/qt/6.10/6.10.2/single/qt-everywhere-src-6.10.2.tar.xz
    URL_HASH MD5=c888eeca204d8ee144d9c57c2df9f8f4
)
FetchContent_MakeAvailable(Qt6)

# We need to build the basic tools (mainly `moc`) first or else Qt's modules compilation fails
if (NOT EXISTS "${CMAKE_BINARY_DIR}/_deps/qt6-build/libexec/moc")
    execute_process(COMMAND ${CMAKE_COMMAND} --build "${CMAKE_BINARY_DIR}/_deps/qt6-build" --parallel)
endif()

# this is so the Qt modules can find and use the basic tools
list(APPEND CMAKE_PREFIX_PATH "${CMAKE_BINARY_DIR}/_deps/qt6-build/lib/cmake/")


set(ARCHIVE_PATH "${CMAKE_BINARY_DIR}/${PROJECT_NAME}-${PROJECT_VERSION}-${CMAKE_HOST_SYSTEM_NAME}-${CMAKE_HOST_SYSTEM_PROCESSOR}.tar.gz")
add_custom_target(package
    COMMAND ${CMAKE_COMMAND} --install .
    COMMAND ${CMAKE_COMMAND} -E tar cfz ${ARCHIVE_PATH} --format=gnutar ${CMAKE_INSTALL_PREFIX}
    COMMENT "Generating .tar.gz ${ARCHIVE_PATH}"
    USES_TERMINAL
)
