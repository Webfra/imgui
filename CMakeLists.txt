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
add_library( ImGui OBJECT )
# ===========================================================================
if ( ${ImGui_Create_Static_Lib} )
    add_library( ImGuiStatic STATIC )
    target_link_libraries( ImGuiStatic PRIVATE ImGui )
endif()
# ===========================================================================
if( ${ImGui_Create_Shared_Lib} )
    add_library( ImGuiShared SHARED )
    target_link_libraries( ImGuiShared PRIVATE ImGui )
endif()
# ===========================================================================
target_include_directories( ImGui PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} )
target_sources( ImGui PRIVATE
        # Source files
        imgui.cpp
        imgui_draw.cpp
        imgui_tables.cpp
        imgui_widgets.cpp
        # Header files (Optional, for IDE support)
        imgui.h
        imconfig.h
        imgui_internal.h
        imstb_rectpack.h
        imstb_textedit.h
        imstb_truetype.h
    )
# ===========================================================================
if( ${ImGui_Add_DemoWindow} )
    target_sources( ImGui PRIVATE imgui_demo.cpp)
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_SDL} )
    # --------------------------------------------------------------------------
    target_include_directories( ImGui PUBLIC ${SDL2_INCLUDE_DIR} ${CMAKE_CURRENT_LIST_DIR}/backends )
    target_link_libraries(      ImGui PUBLIC ${SDL2_LIBRARY} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            backends/imgui_impl_sdl.h
            backends/imgui_impl_sdl.cpp
        )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_Win32} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            backends/imgui_impl_win32.h
            backends/imgui_impl_win32.cpp
        )
    target_link_libraries( ImGui PUBLIC XInput )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_GLFW} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
            backends/imgui_impl_glfw.h
            backends/imgui_impl_glfw.cpp
        )
    # Current MinGW provided version 3.3.2 of glfw does not work with vulkan.
    # This uses glfw3 version 3.4.0 (from github.)
    if(1)
        set(GLFW_VERSION 3.4.0)
        set(GLFW_DIR ../glfw-${GLFW_VERSION}/include)
        set(GLFW_LIB ../build-glfw-${GLFW_VERSION}-Desktop-${CMAKE_BUILD_TYPE}/src/libglfw3.a)
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
        backends/imgui_impl_dx9.h
        backends/imgui_impl_dx9.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D9 )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_DirectX10} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        backends/imgui_impl_dx10.h
        backends/imgui_impl_dx10.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D10 )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_DirectX11} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        backends/imgui_impl_dx11.h
        backends/imgui_impl_dx11.cpp
        )
    target_link_libraries( ImGui PUBLIC D3D11 d3dcompiler )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_OpenGL3} )
    # --------------------------------------------------------------------------
    target_link_libraries( ImGui PUBLIC glad )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        backends/imgui_impl_opengl3.h
        backends/imgui_impl_opengl3.cpp
        )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Renderer_Vulkan} )
    # --------------------------------------------------------------------------
    target_sources( ImGui PRIVATE
        backends/imgui_impl_vulkan.h
        backends/imgui_impl_vulkan.cpp
        )
    #target_compile_definitions( ImGui PUBLIC -DVK_PROTOTYPES -DWIN32_LEAN_AND_MEAN )
    find_library(VULKAN_LIBRARY NAMES vulkan )
    target_link_libraries( ImGui PUBLIC ${VULKAN_LIBRARY} )
    # --------------------------------------------------------------------------
endif()
# ===========================================================================
if( ${ImGui_Add_Windowing_SDL} )
    if( ${ImGui_Add_Renderer_OpenGL3} )
        set ( ImGui_EXAMPLE_SDL_OPENGL_MAIN  OFF CACHE BOOL "Add SDL/OpenGL demo Application?")
        if( ${ImGui_EXAMPLE_SDL_OPENGL_MAIN} )
            add_executable( example_sdl_opengl3
                    examples/example_sdl_opengl3/main.cpp
                )
            target_link_libraries( example_sdl_opengl3 PUBLIC ImGui )
        endif()
    endif()
endif()
