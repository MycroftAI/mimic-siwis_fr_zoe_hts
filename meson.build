project('mimic-siwis_fr_zoe_hts', 'c', default_options : ['c_std=c99'], version: '0')
voice_name = 'siwis_fr_zoe_hts'

build_plugin = get_option('plugin')

sources = [
  'src/siwis_fr_zoe_hts.c',
]

install_data('voices/siwis_fr_zoe_hts.htsvoice',
             install_dir : join_paths(get_option('datadir'), 'ttsmimic', 'voices'))

includes = [include_directories('include')]

# French Dependency:
mimicfrench = dependency('ttsmimic_french', required: false)
if mimicfrench.found()
  mimiccore = dependency('ttsmimic_core')
  mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
else
  # We compile it:
  mimic_french_sub = subproject('mimic-french')
  mimicfrench = mimic_french_sub.get_variable('ttsmimic_french_dep')
  mimiccore = mimic_french_sub.get_variable('mimiccore')
  mimic_plugin_dir = mimic_french_sub.get_variable('mimic_plugin_dir')
  includes += mimic_french_sub.get_variable('includes')
endif

message('Build mimic-@0@ plugin: @1@'.format(voice_name, build_plugin))


fr_header_subdir = join_paths('ttsmimic', 'lang', 'fr')

voice_lib = library('ttsmimic_@0@'.format(voice_name), sources,
                    include_directories : includes,
                    dependencies: [mimicfrench, mimiccore],
                    install: true)

voice_dep = declare_dependency(link_with : voice_lib, 
                               include_directories : includes)


if build_plugin
  voice_plugin_lib = library(
    'ttsmimic_@0@_plugin'.format(voice_name),
    ['src/@0@_plugin.c'.format(voice_name)],
    include_directories : includes,
    dependencies: [mimiccore, mimicfrench, voice_dep],
    install_dir: mimic_plugin_dir,
    install: true)

  voice_plugin_dep = declare_dependency(
    link_with : voice_plugin_lib, 
    include_directories : includes)
endif



############ Headers #################

ttsmimic_headers = ['include/@0@.h'.format(voice_name)]

install_headers(ttsmimic_headers, subdir : fr_header_subdir)


########### pkg-config #############

pkg = import('pkgconfig')

pkg.generate(libraries : voice_lib,
             name : 'ttsmimic_@0@'.format(voice_name),
             subdirs: fr_header_subdir,
             requires: ['ttsmimic_french', 'ttsmimic_core'],
             description: '@0@ support for text to speech synthesis in mimic.'.format(voice_name),
             version: meson.project_version())

