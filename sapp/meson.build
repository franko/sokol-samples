sokol_dep = dependency('sokol')
libgl_dep = dependency('opengl')

sokol_sapp_deps = [sokol_dep, libgl_dep]

cc = meson.get_compiler('c')

if host_machine.system() == 'linux'
    sokol_sapp_deps += dependency('x11')
    sokol_sapp_deps += dependency('xcursor')
    sokol_sapp_deps += dependency('xi')
    sokol_sapp_deps += cc.find_library('m')
    sokol_sapp_deps += cc.find_library('dl')
endif

sokol_shdc = find_program('sokol-shdc')

sokol_target = 'glcore33'

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

shapes_glsl_h = custom_target('shapes-glsl-h',
    input : 'shapes-sapp.glsl',
    output : 'shapes-sapp.glsl.h',
    command : [sokol_shdc, '--input', '@INPUT@', '--output', '@OUTPUT0@', '--slang', sokol_sapp_slang]
)

executable('shapes-sapp', ['shapes-sapp.c', shapes_glsl_h],
    c_args : sokol_sapp_cargs,
    dependencies : sokol_sapp_deps,
    include_directories : sokol_libs_include,
    win_subsystem : 'windows'
)
