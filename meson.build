project('speech-denoiser','c',default_options: 'c_std=c99')

#shared object name
lv2_name = 'sdenoise'

#source to compile
src = 'src/sdenoise.c'

#get compiler
cc = meson.get_compiler('c')

#handling rnnoise static library
lib_rnnoise = cc.find_library('rnnoise',dirs: meson.current_source_dir() + '/rnnoise/.libs/',required : true)
inc_rnnoise = include_directories('rnnoise/include')

#dependencies for speech denoise
m_dep = cc.find_library('m', required : true)
lv2_dep = dependency('lv2', required : true)
nr_dep = [m_dep,lv2_dep,lib_rnnoise]

#compiler optimization flags
if meson.get_compiler('c').get_id() == 'clang'
  add_global_arguments('-mrecip', language : 'c')
endif
cflags = ['-msse','-msse2','-mfpmath=sse','-ffast-math','-fomit-frame-pointer','-fno-finite-math-only']

#install folder
install_folder = 'sdenoise.lv2'

#get the build operating system and configure install path and shared object extension
current_os = build_machine.system()
if current_os == 'darwin' #mac
    extension = '.dylib'
else #unix like
    extension = '.so'
endif #windows
if current_os == 'windows'
    extension = '.dll'
endif

#build of the shared object
shared_library(lv2_name,src,name_prefix: '',include_directories: inc_rnnoise,dependencies: nr_dep,c_args: cflags,install: true,install_dir : install_folder)

#Configure manifest ttl in order to replace the correct shared object extension
manifest_conf = configuration_data()
manifest_conf.set('LIB_EXT',extension)
configure_file(input : 'lv2ttl/manifest.ttl.in',output : 'manifest.ttl',configuration : manifest_conf)

#add manifest.ttl and nrepel.ttl to be installed with the shared object
install_data(['build/manifest.ttl', 'lv2ttl/sdenoise.ttl'],install_dir : install_folder)
