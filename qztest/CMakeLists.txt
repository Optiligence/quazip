project(qztest)

aux_source_directory(. SRCS)

find_package(Qt5 COMPONENTS Network Test)
if(NOT TARGET QuaZip::QuaZip)
	find_package(QuaZip REQUIRED)
endif()

add_executable(${PROJECT_NAME} ${SRCS})
target_link_libraries(${PROJECT_NAME}
	QuaZip::QuaZip
	Qt5::Network
	Qt5::Test
)
