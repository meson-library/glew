project('glew', ['c'], 
         version       : '2.1.0',  
         license       : ['MIT', 'BSD3'],
         meson_version : '>= 0.49.0')




name = 'glew'




system     = build_machine.system()
cpu        = build_machine.cpu()
cpu_family = build_machine.cpu_family()

cc    = meson.get_compiler('c')
cc_id = cc.get_id() 

lib_type    = get_option('default_library')
build_utils = get_option('build_utils')

includes  = []
sources   = []
c_args    = []
link_args = []

supported_compilers = ['msvc', 'clang-cl']


if system not in ['windows']
    error('Script (meson.build) not prepared for your platform yet.')
endif

if system == 'windows' and cc_id not in supported_compilers
    error('Script (meson.build) not prepared for your compiler yet.')
endif




if system == 'windows'
    windows  = import('windows')
    sources += windows.compile_resources('build/glew.rc')

    opengl_dep = cc.find_library('opengl32', required: true)
endif




include = include_directories('include')

sources = ['include/GL/wglew.h',
           'include/GL/glxew.h',
           'include/GL/glew.h',
           'src/glew.c']

c_args    = ['-DGLEW_NO_GLU']




if lib_type == 'static' or lib_type == 'both' 
    static_lib_name        = name
    static_lib_name_prefix = []
    static_lib_name_suffix = []

    if system == 'windows' and cc_id in supported_compilers 
        static_lib_name       += '_static'
        static_lib_name_prefix = ''
        static_lib_name_suffix = 'lib'
    endif

    glew_static = static_library(static_lib_name,
                                 name_prefix         : static_lib_name_prefix, 
                                 name_suffix         : static_lib_name_suffix,
                                 include_directories : include,
                                 sources             : sources,
                                 c_args              : c_args + ['-DGLEW_STATIC'], 
                                 link_args           : link_args,
                                 install             : true)

    glew_static_dep = declare_dependency(include_directories : include,
                                         compile_args        : ['-DGLEW_STATIC'],
                                         link_with           : glew_static)
endif

if lib_type == 'shared' or lib_type == 'both'
    shared_lib_name = name 

    glew_shared = shared_library(shared_lib_name, 
                                 include_directories : include,
                                 sources             : sources,
                                 c_args              : c_args + ['-DGLEW_BUILD'], 
                                 link_args           : link_args,   
                                 dependencies        : [opengl_dep],
                                 install             : true)

    glew_shared_dep = declare_dependency(include_directories : include,
                                         link_with           : glew_shared,
                                         dependencies        : opengl_dep)
endif




if lib_type == 'static'
    glew_dep = glew_static_dep
elif lib_type == 'shared' or lib_type == 'both'
    glew_dep = glew_shared_dep
endif




if build_utils
    executable('glewinfo' 
               ,sources      : ['src/glewinfo.c'] 
               ,c_args       : c_args            
               ,dependencies : [glew_dep]
               ,install      : true)

    executable('visualinfo' 
               ,sources      : ['src/visualinfo.c']
               ,c_args       : c_args
               ,dependencies : [glew_dep]
               ,install      : true)
endif




install_headers('include/GL/wglew.h', 
                'include/GL/glew.h', 
                'include/GL/glxew.h',
                 subdir : 'GL')

install_data(['doc/glew.txt', 
              'doc/gpl.txt', 
              'doc/khronos.txt', 
              'doc/mesa.txt'],
               rename : ['GLEW.BSD3', 
                         'LICENSE.GPL2', 
                         'KHRONOS.MIT', 
                         'MESA.MIT'],
               install_dir : 'share/licenses/glew')