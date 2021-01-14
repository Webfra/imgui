# ===========================================================================
cmake_minimum_required(VERSION 3.17)
# ===========================================================================
project( ImGui )
# ===========================================================================
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# ===========================================================================
set ( ImGui_Add_DemoWindow         ON  CACHE BOOL "Add demo window to the library?")
# ===========================================================================
set ( ImGui_Add_Renderer_DirectX11 OFF CACHE BOOL "Add DirectX11 Renderer Support?")
set ( ImGui_Add_Renderer_DirectX9  OFF CACHE BOOL "Add DirectX9 Renderer Support?")
set ( ImGui_Add_Renderer_DirectX10 OFF CACHE BOOL "Add DirectX10 Renderer Support?")
set ( ImGui_Add_Renderer_OpenGL3   ON  CACHE BOOL "Add OpenGL Renderer Support?")
set ( ImGui_Add_Renderer_Vulkan    OFF CACHE BOOL "Add Vulkan Renderer Support?")
# ===========================================================================
set ( ImGui_Add_Windowing_SDL      ON  CACHE BOOL "Add SDL Windowing Support?")
set ( ImGui_Add_Windowing_Win32    OFF CACHE BOOL "Add Win32 Windowing Support?")
set ( ImGui_Add_Windowing_GLFW     OFF CACHE BOOL "Add GLFW Windowing Support?")
# ===========================================================================
set ( ImGui_Create_Static_Lib      OFF CACHE BOOL "Create a static library? (libImGuiStatic.a")
set ( ImGui_Create_Shared_Lib      OFF CACHE BOOL "Create a shared library? (libImGuiShared.dll)")
# ===========================================================================
set ( SRC ${CMAKE_CURRENT_LIST_DIR} )
# ===========================================================================
add_library( ImGui OBJECT )
# ===========================================================================
if ( ${ImGui_Create_Static_Lib} )
    add_library( ImGuiStatic STATIC )
    target_link_libraries( ImGuiStatic PUBLIC ImGui )
endif()
# ===========================================================================
if( ${ImGui_Create_Shared_Lib} )
    add_library( ImGuiShared SHARED )
    target_link_libraries( ImGuiShared PUBLIC ImGui )
endif()
# ===========================================================================
target_include_directories( ImGui PUBLIC ${SRC} )
target_sources( ImGui PRIVATE
        # Source files
        ${SRC}/imgui.cpp
        ${SRC}/imgui_draw.cpp
        ${SRC}/imgui_tables.cpp
        ${SRC}/imgui_widgets.cpp
        # Header files (Optional, for IDE support)
        ${SRC}/imgui.h
        ${SRC}/imconfig.h
        ${SRC}/imgui_internal.h
        ${SRC}/imstb_rectpack.h
        ${SRC}/imstb_textedit.h
        ${SRC}/imstb_truetype.h
    )
# ===========================================================================
if( ${ImGui_Add_DemoWindow} )
    target_sources( ImGui PRIVATE ${SRC}/imgui_demo.cpp)
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_SDL} )
    # --------------------------------------------------------------------------
    if(1)
        include ( ${CMAKE_CURRENT_LIST_DIR}/SDL2/SDL2.cmake )
        target_include_directories( ImGui PUBLIC ${SDL2_INCLUDE_DIR})
        target_link_libraries(      ImGui PUBLIC ${SDL2_LIBRARY} )
    endif()
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            ${SRC}/backends/imgui_impl_sdl.h
            ${SRC}/backends/imgui_impl_sdl.cpp
        )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_Win32} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            ${SRC}/backends/imgui_impl_win32.h
            ${SRC}/backends/imgui_impl_win32.cpp
        )
    target_link_libraries( ImGui PUBLIC XInput )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_GLFW} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            ${SRC}/backends/imgui_impl_glfw.h
            ${SRC}/backends/imgui_impl_glfw.cpp
        )
    # Current MinGW provided version 3.3.2 of glfw does not work with vulkan.
    # This uses glfw3 version 3.4.0 (from github.)
    if(1)
        set(GLFW_VERSION 3.4.0)
        set(GLFW_DIR ${SRC}/../glfw-${GLFW_VERSION}/include)
        set(GLFW_LIB ${SRC}/../build-glfw-${GLFW_VERSION}-Desktop-${CMAKE_BUILD_TYPE}/src/libglfw3.a)
        target_include_directories ( ImGui PUBLIC ${GLFW_DIR} )
        target_link_libraries      ( ImGui PUBLIC ${GLFW_LIB} )
    else()
        find_library( GLFW_LIBRARY NAMES glfw3 )
        target_link_libraries ( ImGui PUBLIC ${GLFW_LIBRARY} )
    endif()
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_DirectX9} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        ${SRC}/backends/imgui_impl_dx9.h
        ${SRC}/backends/imgui_impl_dx9.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D9 )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_DirectX10} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        ${SRC}/backends/imgui_impl_dx10.h
        ${SRC}/backends/imgui_impl_dx10.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D10 )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_DirectX11} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        ${SRC}/backends/imgui_impl_dx11.h
        ${SRC}/backends/imgui_impl_dx11.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D11 d3dcompiler )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_OpenGL3} )
    # --------------------------------------------------------------------------
    set( ImGui_GLAD_DIR ${CMAKE_CURRENT_LIST_DIR}/../glad CACHE PATH "Path to glad OpenGL loader.")
    include ( ${ImGui_GLAD_DIR}/glad.cmake )
    target_link_libraries( ImGui PUBLIC glad )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        ${SRC}/backends/imgui_impl_opengl3.h
        ${SRC}/backends/imgui_impl_opengl3.cpp
        )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_Vulkan} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        ${SRC}/backends/imgui_impl_vulkan.h
        ${SRC}/backends/imgui_impl_vulkan.cpp
        )
    #target_compile_definitions( ImGui PUBLIC -DVK_PROTOTYPES -DWIN32_LEAN_AND_MEAN )
    find_library(VULKAN_LIBRARY NAMES vulkan )
    target_link_libraries( ImGui PUBLIC ${VULKAN_LIBRARY} )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
