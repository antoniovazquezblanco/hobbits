#
# Config Header
#

set(HOBBITS_GUI_VERSION "${BUILD_VERSION}")
set(CONFIG_FILE_NAME "${CMAKE_CURRENT_SOURCE_DIR}/hobbitsguiconfig.h")
configure_file("${CONFIG_FILE_NAME}.in" "${CONFIG_FILE_NAME}")


#
# Build
#

file(GLOB_RECURSE SRCFILES "${CMAKE_CURRENT_SOURCE_DIR}/*.c" "${CMAKE_CURRENT_SOURCE_DIR}/*.cpp")
file(GLOB_RECURSE HDRFILES "${CMAKE_CURRENT_SOURCE_DIR}/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/*.hpp")
file(GLOB RCFILES "${CMAKE_CURRENT_SOURCE_DIR}/*.qrc" "${CMAKE_CURRENT_SOURCE_DIR}/*.ui")

add_executable("hobbits" "${SRCFILES}" "${HDRFILES}" "${RCFILES}")
target_link_libraries("hobbits" hobbits-python hobbits-widgets hobbits-core Qt5::Widgets)
#harden("hobbits")

if (SELF_CONTAINED_APP)
    set_target_properties("hobbits"
                          PROPERTIES INSTALL_RPATH "$ORIGIN;$ORIGIN/../lib;$ORIGIN/../python/lib")
endif()

# in dev mode, make sure dynamic link is to targetted python
if(DEVELOPMENT_BUILD)
    set_target_properties("hobbits"
                          PROPERTIES BUILD_RPATH "${Python3_LIBRARY_DIRS}")
endif()

#
# CPack
#

list(APPEND CPACK_PACKAGE_EXECUTABLES "hobbits;Hobbits")
list(APPEND CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "${CMAKE_INSTALL_FULL_DATADIR}/icons/hicolor/624x624/apps")
list(APPEND CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "${CMAKE_INSTALL_FULL_DATADIR}/icons/hicolor/624x624")
list(APPEND CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "${CMAKE_INSTALL_FULL_DATADIR}/icons/hicolor")
list(APPEND CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "${CMAKE_INSTALL_FULL_DATADIR}/icons")
list(APPEND CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION "${CMAKE_INSTALL_FULL_DATADIR}/applications")

install(TARGETS "hobbits" COMPONENT "runtime")
install(FILES
	"${CMAKE_CURRENT_SOURCE_DIR}/Hobbits.desktop"
	DESTINATION ${CMAKE_INSTALL_DATADIR}/applications
        COMPONENT "runtime"
)
install(FILES
	"${CMAKE_CURRENT_SOURCE_DIR}/images/icons/HobbitsRingSmall.png"
	DESTINATION ${CMAKE_INSTALL_DATADIR}/icons/hicolor/624x624/apps/
        COMPONENT "runtime"
)

pass_through_cpack_vars()