project(QuaZip_Library VERSION ${QUAZIP_LIB_VERSION})

aux_source_directory(. SRCS)
file(GLOB PUBLIC_HEADERS "*.h")

add_library(${QUAZIP_LIB_TARGET_NAME} ${SRCS})
set_target_properties(${QUAZIP_LIB_TARGET_NAME} PROPERTIES VERSION ${QUAZIP_LIB_VERSION} SOVERSION ${QUAZIP_LIB_SOVERSION})
target_include_directories(${QUAZIP_LIB_TARGET_NAME} PUBLIC 
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/${QUAZIP_LIB_TARGET_NAME}>
)
target_link_libraries(${QUAZIP_LIB_TARGET_NAME} ${QUAZIP_QTCORE_LIBRARIES} ZLIB::ZLIB)

if(BUILD_SHARED_LIBS)
	target_compile_definitions(${QUAZIP_LIB_TARGET_NAME} PRIVATE QUAZIP_BUILD) # dllexport
else()
	target_compile_definitions(${QUAZIP_LIB_TARGET_NAME} PUBLIC QUAZIP_STATIC) # suppress dllimport
endif()

function(create_cmake_config _target _config_in)
	include(CMakePackageConfigHelpers)
	write_basic_package_version_file(${PROJECT_BINARY_DIR}/${_target}ConfigVersion.cmake
		COMPATIBILITY AnyNewerVersion
	)
	configure_package_config_file(${_config_in} ${_target}Config.cmake
		INSTALL_DESTINATION lib/cmake/${_target}
	)

	include(GNUInstallDirs)
	install(TARGETS ${_target}
		EXPORT ${_target}Targets
		RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
		LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
		ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	)
	install(EXPORT ${_target}Targets
		NAMESPACE ${_target}::
		DESTINATION lib/cmake/${_target}
	)
	install(FILES ${PROJECT_BINARY_DIR}/${_target}Config.cmake
				  ${PROJECT_BINARY_DIR}/${_target}ConfigVersion.cmake
		DESTINATION lib/cmake/${_target}
	)
endfunction()

create_cmake_config(${QUAZIP_LIB_TARGET_NAME} QuaZipConfig.cmake.in)
install(FILES ${PUBLIC_HEADERS} DESTINATION "include/${QUAZIP_LIB_TARGET_NAME}")
