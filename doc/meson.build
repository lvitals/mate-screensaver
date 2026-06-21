mate_screensaver_xml = configure_file(
  input: 'mate-screensaver.xml.in',
  output: 'mate-screensaver.xml',
  configuration: conf_data
)

if get_option('docbook-docs')
  xmlto = find_program('xmlto', required: true)
  custom_target('mate-screensaver-html',
    input: [mate_screensaver_xml, 'dbus-interface.xml'],
    output: 'mate-screensaver.html',
    command: [
      xmlto,
      'html-nochunks',
      '-m', meson.current_source_dir() / 'config.xsl',
      '-o', '@OUTDIR@',
      '@INPUT0@',
    ],
    install: true,
    install_dir: docdir
  )
endif
