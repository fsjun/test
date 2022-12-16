cmake_minimum_required (VERSION 3.11)

if(UNIX AND NOT APPLE)
    set(LINUX TRUE)
endif()

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED on)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
project (test)

if(WIN32)

include_directories(D:/program/cpp/include)
LINK_DIRECTORIES(D:/program/cpp/lib)

#include_directories(D:/program/vcpkg/installed/x64-windows/include)
#LINK_DIRECTORIES(D:/program/vcpkg/installed/x64-windows/debug/lib)
#LINK_DIRECTORIES(D:/program/vcpkg/installed/x64-windows/debug/lib/manual-link)
include_directories(D:/program/vcpkg/installed/x64-windows-static/include)
LINK_DIRECTORIES(D:/program/vcpkg/installed/x64-windows-static/debug/lib)
LINK_DIRECTORIES(D:/program/vcpkg/installed/x64-windows-static/debug/lib/manual-link)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /bigobj /wd4430 /utf-8")

if (MSVC)
    set(CompilerFlags
        CMAKE_CXX_FLAGS
        CMAKE_CXX_FLAGS_DEBUG
        CMAKE_CXX_FLAGS_RELEASE
        CMAKE_C_FLAGS
        CMAKE_C_FLAGS_DEBUG
        CMAKE_C_FLAGS_RELEASE
        )
    foreach(CompilerFlag ${CompilerFlags})
        string(REPLACE "/MD" "/MT" ${CompilerFlag} "${${CompilerFlag}}")
    endforeach()
endif(MSVC)

message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
message(STATUS "CMAKE_CXX_FLAGS_DEBUG: ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
message(STATUS "CMAKE_C_FLAGS_DEBUG: ${CMAKE_C_FLAGS_DEBUG}")
add_definitions(-DWIN32_LEAN_AND_MEAN -D_WIN32_WINNT=0x0601)

elseif(APPLE)

include_directories(/usr/local/3rd/cpp/include)
LINK_DIRECTORIES(/usr/local/3rd/cpp/lib)

include_directories(/root/app/vcpkg/installed/x64-linux/include)
LINK_DIRECTORIES(/root/app/vcpkg/installed/x64-linux/debug/lib)
LINK_DIRECTORIES(/root/app/vcpkg/installed/x64-linux/debug/lib/manual-link)
include_directories("/usr/local/include")
LINK_DIRECTORIES(/usr/local/lib)

elseif(LINUX)

include_directories(/usr/local/3rd/cpp/include)
LINK_DIRECTORIES(/usr/local/3rd/cpp/lib)

include_directories(/root/app/vcpkg/installed/x64-linux/include)
LINK_DIRECTORIES(/root/app/vcpkg/installed/x64-linux/debug/lib)
LINK_DIRECTORIES(/root/app/vcpkg/installed/x64-linux/debug/lib/manual-link)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -O0 -ggdb")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wl,-Bstatic -lstdc++ -Wl,-Bdynamic")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static")
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wl,--whole-archive -lpthread -Wl,--no-whole-archive")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -static-libstdc++ -static-libgcc")
else()
endif()

ADD_DEFINITIONS(-DBOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX -DBOOST_ASIO_DISABLE_SMALL_BLOCK_RECYCLING)

include_directories(src)

set(MODULE_NAME test)
file(GLOB_RECURSE SRC_LIST src/*.h src/*.cpp)
ADD_EXECUTABLE(${MODULE_NAME} ${SRC_LIST})
if(WIN32)
	TARGET_LINK_LIBRARIES(${MODULE_NAME} httpclient wsclient jsoncc cppcommon jsoncpp boost_random-vc140-mt-gd boost_program_options-vc140-mt-gd boost_system-vc140-mt-gd boost_filesystem-vc140-mt-gd boost_thread-vc140-mt-gd libssl libcrypto)
elseif(APPLE)
	TARGET_LINK_LIBRARIES(${MODULE_NAME} httpserver wsserver database ossl mysqlcppconn8-static resolv jsoncc cppcommon jsoncpp boost_random boost_program_options boost_system boost_filesystem boost_thread ssl crypto pthread dl)
else()
	TARGET_LINK_LIBRARIES(${MODULE_NAME} httpserver wsserver database ossl mysqlcppconn8-static resolv jsoncc cppcommon jsoncpp boost_random boost_program_options boost_system boost_filesystem boost_thread ssl crypto pthread dl)
endif()

