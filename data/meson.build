install_man([
  'mate-screensaver.1',
  'mate-screensaver-command.1',
  'mate-screensaver-preferences.1',
])

i18n.merge_file(
  input: 'mate-screensaver-preferences.desktop.in',
  output: 'mate-screensaver-preferences.desktop',
  type: 'desktop',
  po_dir: po_dir,
  install: true,
  install_dir: datadir / 'applications'
)

i18n.merge_file(
  input: 'mate-screensaver.directory.desktop.in',
  output: 'mate-screensaver.directory',
  type: 'desktop',
  po_dir: po_dir,
  install: true,
  install_dir: datadir / 'desktop-directories'
)

configure_file(
  input: 'mate-screensavers.menu.in',
  output: 'mate-screensavers.menu',
  copy: true,
  install: true,
  install_dir: sysconfdir / 'xdg' / 'menus'
)

install_data('lock-dialog-default.ui', install_dir: privdatadir)

configure_file(
  input: 'mate-screensaver.pc.in',
  output: 'mate-screensaver.pc',
  configuration: conf_data,
  install: true,
  install_dir: libdir / 'pkgconfig'
)

configure_file(
  input: 'org.mate.ScreenSaver.service.in',
  output: 'org.mate.ScreenSaver.service',
  configuration: conf_data,
  install: true,
  install_dir: dbus_session_service_dir
)

configure_file(
  input: 'org.mate.screensaver.gschema.xml.in',
  output: 'org.mate.screensaver.gschema.xml',
  configuration: conf_data,
  install: true,
  install_dir: schemadir
)

if auth_scheme == 'pam'
  install_data('mate-screensaver', install_dir: pam_prefix / 'pam.d')
endif

subdir('images')
