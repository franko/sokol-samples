sokol_target = 'glcore33'

cc = meson.get_compiler('c')

if host_machine.system() == 'windows'
    sokol_sys_libs = ['kernel32', 'user32', 'shell32', 'gdi32']
    if sokol_target == 'd3d11'
        sokol_sys_libs += ['dxgi', 'd3d11']
    endif
    sokol_dep_names = ['sokol']
elif host_machine.system() == 'linux'
    sokol_sys_libs = ['m', 'dl']
    sokol_dep_names = ['opengl', 'x11', 'xcursor', 'xi', 'sokol']
endif

sokol_sapp_deps = []
foreach lib : sokol_sys_libs
    sokol_sapp_deps += cc.find_library(lib)
endforeach

foreach dep_name : sokol_dep_names
    sokol_sapp_deps += dependency(dep_name)
endforeach

sokol_shdc = find_program('sokol-shdc')

# https://github.com/floooh/sokol-tools/blob/master/docs/sokol-shdc.md
sokol_defs = {
    'gles3':    ['-DSOKOL_GLES3',    'glsl330'],             # emscripten
    'gles2':    ['-DSOKOL_GLES2',    'glsl100'],             # raspberry
    'gles3es':  ['-DSOKOL_GLES3',    'glsl300es'],           # android
    'd3d11':    ['-DSOKOL_D3D11',    'hlsl5'],               # windows
    'ios':      ['-DSOKOL_METAL',    'metal_ios:metal_sim'], # ios
    'macos':    ['-DSOKOL_METAL',    'metal_macos'],         # macos
    'glcore33': ['-DSOKOL_GLCORE33', 'glsl330'],             # desktop
}

sokol_sapp_cargs = ['-DSOKOL_IMPL', sokol_defs[sokol_target][0]]
sokol_sapp_slang = sokol_defs[sokol_target][1]

sokol_apps = [
    {'name' : 'shapes',    'shader' : 'shapes-sapp.glsl',    'sources' : ['shapes-sapp.c']},
    {'name' : 'cube',      'shader' : 'cube-sapp.glsl',      'sources' : ['cube-sapp.c']},
    {'name' : 'offscreen', 'shader' : 'offscreen-sapp.glsl', 'sources' : ['offscreen-sapp.c']},
    {'name' : 'sdf',       'shader' : 'sdf-sapp.glsl',       'sources' : ['sdf-sapp.c']},
]

foreach app : sokol_apps
    glsl_h = custom_target(app['shader'],
        input : app['shader'],
        output : app['shader'] + '.h',
        command : [sokol_shdc, '--input', '@INPUT@', '--output', '@OUTPUT0@', '--slang', sokol_sapp_slang]
    )
    if app['name'] == 'cube'
        sokol_cube_shader = glsl_h
    endif
    executable(app['name'] + '-sapp', app['sources'] + [glsl_h],
        c_args : sokol_sapp_cargs,
        dependencies : sokol_sapp_deps,
        include_directories : sokol_libs_include,
        win_subsystem : 'windows'
    )
endforeach

sdl_dep = dependency('sdl2', method : 'config-tool')

executable('cube-sdl2', ['cube-sdl2.c'] + [sokol_cube_shader],
    c_args : sokol_sapp_cargs,
    dependencies : sokol_sapp_deps + [sdl_dep],
    include_directories : sokol_libs_include,
    win_subsystem : 'windows'
)

cimgui_dep = dependency('cimgui')

executable('cimgui-sapp', 'cimgui-sapp.c',
    c_args : sokol_sapp_cargs,
    dependencies : sokol_sapp_deps + [cimgui_dep],
    include_directories : sokol_libs_include,
    win_subsystem : 'windows'
)


pixelformats_glsl_h = custom_target('pixelformats-glsl',
    input : 'pixelformats-sapp.glsl',
    output : 'pixelformats-sapp.glsl.h',
    command : [sokol_shdc, '--input', '@INPUT@', '--output', '@OUTPUT0@', '--slang', sokol_sapp_slang]
)

executable('pixelformats-sapp', ['pixelformats-sapp.c', pixelformats_glsl_h],
    c_args : sokol_sapp_cargs,
    dependencies : sokol_sapp_deps + [cimgui_dep],
    include_directories : sokol_libs_include,
    win_subsystem : 'windows'
)

imgui_dep = dependency('imgui')
imgui_demo_dep = dependency('imguidemo')

executable('imgui-highdpi-sapp', 'imgui-highdpi-sapp.cc',
    cpp_args : sokol_sapp_cargs,
    dependencies : sokol_sapp_deps + [imgui_dep, imgui_demo_dep],
    include_directories : sokol_libs_include,
    win_subsystem : 'windows'
)

