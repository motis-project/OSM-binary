#protobuf_generate_cpp(CPPS HS fileformat.proto osmformat.proto)
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/fileformat.pb.h
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/fileformat.pb.cc
  COMMAND ${CMAKE_CROSSCOMPILING_EMULATOR} $<TARGET_FILE:protoc>
    --cpp_out=${CMAKE_CURRENT_BINARY_DIR}
    --proto_path=${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/fileformat.proto .proto
  DEPENDS protoc
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/gtfsrt.proto
  COMMENT "Generating GTFS RT protocol buffers C++ code"
)


add_library(osmpbf STATIC ${CMAKE_CURRENT_BINARY_DIR}/fileformat.pb.cc)
target_include_directories(osmpbf SYSTEM PUBLIC ${Protobuf_INCLUDE_DIRS})
install(TARGETS osmpbf ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})

add_library(osmpbf_shared SHARED ${CMAKE_CURRENT_BINARY_DIR}/fileformat.pb.cc)
target_link_libraries(osmpbf_shared PRIVATE protobuf::libprotobuf)
target_include_directories(osmpbf_shared SYSTEM PUBLIC ${Protobuf_INCLUDE_DIRS})
set_target_properties(osmpbf_shared PROPERTIES OUTPUT_NAME osmpbf
                                               VERSION ${PROJECT_VERSION}
                                               SOVERSION ${PROJECT_VERSION_MAJOR})
install(TARGETS osmpbf_shared LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(FILES ${CMAKE_SOURCE_DIR}/include/osmpbf/osmpbf.h
              ${CMAKE_BINARY_DIR}/osmpbf/osmformat.pb.h
              ${CMAKE_BINARY_DIR}/osmpbf/fileformat.pb.h
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/osmpbf)

