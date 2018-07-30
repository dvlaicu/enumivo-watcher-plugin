file(GLOB HEADERS "include/enumivo/watcher_plugin/*.hpp")
add_library( watcher_plugin
             watcher_plugin.cpp
             ${HEADERS} include/enumivo/watcher_plugin/watcher_plugin.hpp
                        include/enumivo/watcher_plugin/http_async_client.hpp)

target_link_libraries( watcher_plugin chain_plugin enumivo_chain appbase fc )
target_include_directories( watcher_plugin PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include" )
