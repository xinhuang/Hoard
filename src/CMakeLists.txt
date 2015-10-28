project(libhoard)

set(MAIN_SRC source/libhoard.cpp)
set(UNIX_SRC ${MAIN_SRC} source/unixtls.cpp)
set(SUNW_SRC ${UNIX_SRC} Heap-Layers/wrappers/wrapper.cpp)
set(GNU_SRC ${UNIX_SRC} Heap-Layers/wrappers/gnuwrapper.cpp)
set(MACOS_SRC ${MAIN_SRC} Heap-Layers/wrappers/macwrapper.cpp source/mactls.cpp)

include_directories(. include include/util include/hoard include/superblocks Heap-Layers)

if (UNIX)
  include_directories(/usr/include/nptl)
  add_definitions(-Wall -Wextra -Wshadow -Wconversion -ffast-math -g -fno-builtin-malloc -finline-limit=20000 -finline-functions -Bsymbolic -Wl,--no-as-needed -ldl)   #-pipe
  add_definitions(-DNDEBUG -D_REENTRANT=1)
  if (bitness EQUAL 4)
    add_definitions(-malign-double)
  else ()
    add_definitions(-fPIC)
  endif ()

  add_library(libhoard SHARED ${GNU_SRC})
  target_link_libraries(libhoard dl)

elseif (MSVC)

  # WIN_INCLUDES = /I. /Iinclude /Iinclude/util /Iinclude/hoard /Iinclude/superblocks /IHeap-Layers
  # WIN_DEFINES = /D "NDEBUG" /D "_WINDOWS" /D "_WINDLL" /D "_WINRT_DLL" /D "_UNICODE" /D "UNICODE"
  # WIN_DEBUG_DEFINES = /D "_WINDOWS" /D "_WINDLL" /D "_WINRT_DLL" /D "_UNICODE" /D "UNICODE"
  #
  # WIN_FLAGS         = /Zi /Ox /MD /nologo /W1 /WX- /Ox /Oi /Oy- /Gm- /EHsc /MD /GS /Gy /Zc:wchar_t /Zc:forScope /Gd /errorReport:queue
  # WIN_DEBUG_FLAGS   = /Zi /MD /nologo /W1 /WX- /Gm- /EHsc /MD /GS /Gy /Zc:wchar_t /Zc:forScope /Gd /errorReport:queue

  # cl $(WIN_INCLUDES) $(WIN_DEFINES) $(WIN_FLAGS) "source\libhoard.cpp" "Heap-Layers\wrappers\winwrapper.cpp" "source\wintls.cpp" /GL /link /DLL /subsystem:console /OUT:libhoard.dll
  add_library(libhoard SHARED source/libhoard.cpp Heap-Layers/wrappers/winwrapper.cpp source/wintls.cpp)
  # cl $(WIN_INCLUDES) $(WIN_DEFINES) $(WIN_FLAGS) /c "source\uselibhoard.cpp"
  add_library(uselibhoard STATIC source/uselibhoard.cpp)
  target_link_libraries(uselibhoard libhoard)
endif()

target_link_libraries(libhoard ${CMAKE_THREAD_LIBS_INIT})

if (NOT "${PERFLIB_ROOT}" STREQUAL "")
  message(STATUS "Will link to perflib at ${PERFLIB_ROOT}")
  set(PERFLIB_INCLUDE_DIR ${PERFLIB_ROOT}/perf/include)
  set(PERFLIB_LIBRARY_DIR ${PERFLIB_ROOT}/build/lib)
  set(PERFLIB_LIBS ${PERFLIB_LIBRARY_DIR}/libperfLib.so)
  include_directories(${PERFLIB_INCLUDE_DIR})
  message("link directories: ${PERFLIB_LIBRARY_DIR}")
  message("perflib: ${PERFLIB_LIBS}")
  target_link_libraries(libhoard ${PERFLIB_LIBS})
endif ()
