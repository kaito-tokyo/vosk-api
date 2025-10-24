cmake_minimum_required(VERSION 3.13)
project(vosk-api CXX)

include(GNUInstallDirs)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_EXTENSIONS OFF)

add_library(
    vosk
    src/language_model.cc
    src/model.cc
    src/recognizer.cc
    src/spk_model.cc
    src/vosk_api.cc
    src/postprocessor.cc
)
set_target_properties(vosk PROPERTIES PUBLIC_HEADER src/vosk_api.h)

target_include_directories(
    vosk
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

find_package(kaldi REQUIRED)
target_link_libraries(
    vosk
    PUBLIC
        kaldi::kaldi-base
        kaldi::kaldi-online2
        kaldi::kaldi-rnnlm
        OpenFst::fstngram
)

install(
    TARGETS vosk
    EXPORT vosk-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(
    EXPORT vosk-targets
    NAMESPACE vosk::
    DESTINATION ${CMAKE_INSTALL_DATADIR}/vosk
)

include(CMakePackageConfigHelpers)
configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/vosk-config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/vosk-config.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_DATADIR}/vosk
)
install(
    FILES ${CMAKE_CURRENT_BINARY_DIR}/vosk-config.cmake
    DESTINATION ${CMAKE_INSTALL_DATADIR}/vosk
)
