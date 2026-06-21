theme_engine_marshal = gnome.genmarshal('gs-theme-engine-marshal',
  sources: 'gs-theme-engine-marshal.list',
  prefix: 'gs_theme_engine_marshal'
)

libgs_theme_engine = static_library('gs-theme-engine',
  sources: [
    'gs-theme-window.c',
    'gs-theme-window.h',
    'gs-theme-engine.c',
    'gs-theme-engine.h',
    theme_engine_marshal,
  ],
  dependencies: mate_screensaver_saver_dep,
  include_directories: root_inc,
  c_args: common_c_args
)

executable('floaters',
  sources: ['floaters.c'],
  dependencies: mate_screensaver_saver_dep + [libm],
  include_directories: root_inc,
  link_with: libgs_theme_engine,
  c_args: common_c_args,
  install: true,
  install_dir: pkglibexecdir
)

executable('popsquares',
  sources: ['gste-popsquares.c', 'gste-popsquares.h', 'popsquares.c'],
  dependencies: mate_screensaver_saver_dep,
  include_directories: root_inc,
  link_with: libgs_theme_engine,
  c_args: common_c_args,
  install: true,
  install_dir: pkglibexecdir
)

executable('slideshow',
  sources: ['gste-slideshow.c', 'gste-slideshow.h', 'xdg-user-dir-lookup.c', 'xdg-user-dir-lookup.h', 'slideshow.c'],
  dependencies: mate_screensaver_saver_dep,
  include_directories: root_inc,
  link_with: libgs_theme_engine,
  c_args: common_c_args,
  install: true,
  install_dir: pkglibexecdir
)

executable('starfield',
  sources: ['gste-starfield.c', 'gste-starfield.h', 'starfield.c'],
  dependencies: mate_screensaver_saver_dep + [libm],
  include_directories: root_inc,
  link_with: libgs_theme_engine,
  c_args: common_c_args,
  install: true,
  install_dir: pkglibexecdir
)

desktop_in_files = [
  'cosmos-slideshow.desktop.in.in',
  'footlogo-floaters.desktop.in.in',
  'gnomelogo-floaters.desktop.in.in',
  'personal-slideshow.desktop.in.in',
  'popsquares.desktop.in.in',
  'starfield.desktop.in.in',
]

desktop_conf = configuration_data()
desktop_conf.set('SLIDESHOW_COSMOS_DIR', slideshow_cosmos_dir)
desktop_conf.set('FLOATERS_FOOT_LOGO_IMAGE', floaters_foot_logo_image)
desktop_conf.set('FLOATERS_FOOT_GNOME_LOGO_IMAGE', floaters_foot_gnome_logo_image)
desktop_conf.set('SAVERDIR', pkglibexecdir)

foreach f : desktop_in_files
  base_name = f.split('.desktop.in.in')[0]
  
  desktop_in = configure_file(
    input: f,
    output: base_name + '.desktop.in',
    configuration: desktop_conf
  )
  
  i18n.merge_file(
    input: desktop_in,
    output: base_name + '.desktop',
    type: 'desktop',
    po_dir: po_dir,
    install: true,
    install_dir: themesdir
  )
endforeach
