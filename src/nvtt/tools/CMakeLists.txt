

ADD_EXECUTABLE(nvcompress compress.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvcompress nvcore nvmath nvimage nvtt)

ADD_EXECUTABLE(nvdecompress decompress.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvdecompress nvcore nvmath nvimage)

ADD_EXECUTABLE(nvddsinfo ddsinfo.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvddsinfo nvcore nvmath nvimage)

ADD_EXECUTABLE(nvimgdiff imgdiff.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvimgdiff nvcore nvmath nvimage)

ADD_EXECUTABLE(nvassemble assemble.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvassemble nvcore nvmath nvimage)

ADD_EXECUTABLE(nvzoom resize.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nvzoom nvcore nvmath nvimage)

ADD_EXECUTABLE(nv-gnome-thumbnailer thumbnailer.cpp cmdline.h)
TARGET_LINK_LIBRARIES(nv-gnome-thumbnailer nvcore nvmath nvimage)
  
INSTALL(TARGETS nvcompress nvdecompress nvddsinfo nvimgdiff nvassemble nvzoom nv-gnome-thumbnailer DESTINATION bin)
 
 # Use gconftool-2 to install gnome thumbnailer
FIND_PROGRAM(GCONFTOOL2 gconftool-2)

IF(GCONFTOOL2)
	CONFIGURE_FILE(nvtt-thumbnailer.schema.in ${CMAKE_CURRENT_BINARY_DIR}/nvtt-thumbnailer.schema)

 	INSTALL(CODE "MESSAGE(STATUS \"Installing thumbnailer schema\")")
 	#gconftool-2 --get-default-source
 	INSTALL(CODE "EXECUTE_PROCESS(COMMAND ${GCONFTOOL2} --get-default-source OUTPUT_VARIABLE GCONF_CONFIG_SOURCE OUTPUT_STRIP_TRAILING_WHITESPACE)")
 	INSTALL(CODE "set(ENV{GCONF_CONFIG_SOURCE} \"\${GCONF_CONFIG_SOURCE}\")")
 	INSTALL(CODE "EXECUTE_PROCESS(COMMAND ${GCONFTOOL2} --makefile-install-rule ${CMAKE_CURRENT_BINARY_DIR}/nvtt-thumbnailer.schema)")
ENDIF(GCONFTOOL2)

# UI tools
IF(QT4_FOUND) # AND NOT MSVC)
	SET(QT_USE_QTOPENGL TRUE)
	INCLUDE_DIRECTORIES(${QT_INCLUDE_DIR} ${CMAKE_CURRENT_BINARY_DIR})
	
	SET(SRCS
		ui/main.cpp
		ui/configdialog.h
		ui/configdialog.cpp)

	SET(LIBS
		nvtt
		${QT_QTCORE_LIBRARY}
		${QT_QTGUI_LIBRARY}
		${QT_QTOPENGL_LIBRARY})

	QT4_WRAP_UI(UICS ui/configdialog.ui)
	QT4_WRAP_CPP(MOCS ui/configdialog.h)
	#QT4_ADD_RESOURCES(RCCS ui/configdialog.rc)

	ADD_EXECUTABLE(nvcompressui MACOSX_BUNDLE ${SRCS} ${UICS} ${MOCS})
	TARGET_LINK_LIBRARIES(nvcompressui ${LIBS})

ENDIF(QT4_FOUND) # AND NOT MSVC)
