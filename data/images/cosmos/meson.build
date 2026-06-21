backgrounds = [
  'blue-marble-west.jpg',
  'cloud.jpg',
  'comet.jpg',
  'earth-horizon.jpg',
  'galaxy-ngc3370.jpg',
  'helix-nebula.jpg',
  'jupiter.jpg',
  'sombrero.jpg',
  'whirlpool.jpg',
]

install_data(backgrounds, install_dir: slideshow_cosmos_dir)

cosmos_conf = configuration_data()
cosmos_conf.set('IMAGESDIR', slideshow_cosmos_dir)

configure_file(
  input: 'background-1.xml.in',
  output: 'background-1.xml',
  configuration: cosmos_conf,
  install: true,
  install_dir: slideshow_cosmos_dir
)

cosmos_xml_in = configure_file(
  input: 'cosmos.xml.in.in',
  output: 'cosmos.xml.in',
  configuration: cosmos_conf
)

i18n.merge_file(
  input: cosmos_xml_in,
  output: 'cosmos.xml',
  data_dirs: [meson.project_source_root()],
  po_dir: po_dir,
  install: true,
  install_dir: datadir / 'mate-background-properties'
)
