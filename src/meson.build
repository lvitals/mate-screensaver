
# GLib resources for preferences
preferences_resources = gnome.compile_resources('mate-screensaver-preferences-resources',
  '../data/org.mate.screensaver.gresource.xml',
  source_dir: '../data',
  c_name: 'screensaver'
)

# GLib Marshallers
marshal_sources = gnome.genmarshal('gs-marshal',
  sources: 'gs-marshal.list',
  prefix: 'gs_marshal'
)

# Authentication source files based on scheme
auth_sources = [
  'gs-auth.h',
  'gs-auth-pam.h',
  'gs-auth-@0@.c'.format(auth_scheme)
]

dialog_link_with = []
if have_passwd_helper
  dialog_link_with += libhelper_proto
endif

# Binaries
executable('mate-screensaver-command',
  sources: ['mate-screensaver-command.c'],
  dependencies: mate_screensaver_command_dep,
  include_directories: root_inc,
  c_args: common_c_args,
  install: true,
  install_dir: bindir
)

executable('mate-screensaver-preferences',
  sources: [
    'mate-screensaver-preferences.c',
    'copy-theme-dialog.c',
    'copy-theme-dialog.h',
    'gs-theme-manager.c',
    'gs-theme-manager.h',
    'gs-job.c',
    'gs-job.h',
    'gs-debug.c',
    'gs-debug.h',
    'subprocs.c',
    'subprocs.h',
    preferences_resources
  ],
  dependencies: mate_screensaver_capplet_dep,
  include_directories: root_inc,
  c_args: common_c_args,
  install: true,
  install_dir: bindir
)

executable('mate-screensaver-dialog',
  sources: [
    'mate-screensaver-dialog.c',
    'gs-lock-plug.c',
    'gs-lock-plug.h',
    'gs-debug.c',
    'gs-debug.h',
    'setuid.c',
    'setuid.h',
    'subprocs.c',
    'subprocs.h',
    auth_sources
  ],
  dependencies: mate_screensaver_dialog_dep + saver_libs_dep + auth_deps + [libnotify_dep, libmatekbdui_dep],
  include_directories: root_inc,
  link_with: dialog_link_with,
  c_args: common_c_args,
  install: true,
  install_dir: libexecdir
)

if have_libgl
  executable('mate-screensaver-gl-helper',
    sources: ['mate-screensaver-gl-helper.c', 'gs-visual-gl.c', 'gs-visual-gl.h'],
    dependencies: mate_screensaver_dep + [libgl_dep, x11_dep],
    include_directories: root_inc,
    c_args: common_c_args,
    install: true,
    install_dir: libexecdir
  )
endif

executable('mate-screensaver',
  sources: [
    'mate-screensaver.c',
    'mate-screensaver.h',
    'gs-monitor.c',
    'gs-monitor.h',
    'gs-watcher-x11.c',
    'gs-watcher.h',
    'gs-listener-dbus.c',
    'gs-listener-dbus.h',
    'gs-manager.c',
    'gs-manager.h',
    'gs-window-x11.c',
    'gs-window.h',
    'gs-prefs.c',
    'gs-prefs.h',
    'gs-theme-manager.c',
    'gs-theme-manager.h',
    'gs-job.c',
    'gs-job.h',
    'gs-debug.c',
    'gs-debug.h',
    'subprocs.c',
    'subprocs.h',
    'gs-grab-x11.c',
    'gs-grab.h',
    'gs-fade.c',
    'gs-fade.h',
    marshal_sources
  ],
  dependencies: mate_screensaver_dep + saver_libs_dep + systemd_deps,
  include_directories: root_inc,
  c_args: common_c_args,
  export_dynamic: true,
  install: true,
  install_dir: bindir
)

# Autostart desktop file
desktop_in = configure_file(
  input: 'mate-screensaver.desktop.in.in',
  output: 'mate-screensaver.desktop.in',
  configuration: conf_data
)

i18n.merge_file(
  input: desktop_in,
  output: 'mate-screensaver.desktop',
  type: 'desktop',
  po_dir: po_dir,
  install: true,
  install_dir: sysconfdir / 'xdg' / 'autostart'
)

# Test binaries
executable('test-fade',
  sources: ['test-fade.c', 'gs-fade.c', 'gs-fade.h', 'gs-debug.c', 'gs-debug.h'],
  dependencies: mate_screensaver_dep + saver_libs_dep,
  include_directories: root_inc,
  c_args: common_c_args,
  install: false
)

executable('test-passwd',
  sources: ['test-passwd.c', 'setuid.c', 'setuid.h', 'subprocs.c', 'subprocs.h', auth_sources],
  dependencies: mate_screensaver_dialog_dep + auth_deps,
  include_directories: root_inc,
  link_with: dialog_link_with,
  c_args: common_c_args,
  install: false
)

executable('test-watcher',
  sources: ['test-watcher.c', 'gs-watcher-x11.c', 'gs-watcher.h', 'gs-debug.c', 'gs-debug.h', marshal_sources],
  dependencies: mate_screensaver_dep + saver_libs_dep,
  include_directories: root_inc,
  c_args: common_c_args,
  install: false
)

executable('test-window',
  sources: ['test-window.c', 'gs-window-x11.c', 'gs-window.h', 'gs-grab-x11.c', 'gs-grab.h', 'gs-debug.c', 'gs-debug.h', 'subprocs.c', 'subprocs.h', marshal_sources],
  dependencies: mate_screensaver_dep + saver_libs_dep,
  include_directories: root_inc,
  c_args: common_c_args,
  install: false
)
