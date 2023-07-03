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