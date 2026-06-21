project('mate-screensaver', 'c',
  version: '1.28.1',
  meson_version: '>=0.59.0',
  default_options: [
    'c_std=gnu11',
    'warning_level=1',
  ]
)

i18n = import('i18n')
gnome = import('gnome')

# Compiler
cc = meson.get_compiler('c')

# Directories
prefix = get_option('prefix')
datadir = prefix / get_option('datadir')
bindir = prefix / get_option('bindir')
libdir = prefix / get_option('libdir')
libexecdir = prefix / get_option('libexecdir')
sysconfdir = prefix / get_option('sysconfdir')
localedir = prefix / get_option('localedir')

pkglibexecdir = libexecdir / meson.project_name()
privdatadir = datadir / meson.project_name()
themesdir = datadir / 'applications' / 'screensavers'
schemadir = datadir / 'glib-2.0' / 'schemas'

# Specific directories and values for configuration
slideshow_cosmos_dir = datadir / 'backgrounds' / 'cosmos'
floaters_foot_logo_image = datadir / 'pixmaps' / 'mate-logo-white.svg'
floaters_foot_gnome_logo_image = datadir / 'pixmaps' / 'gnome-logo-white.svg'

root_inc = include_directories('.')

common_c_args = [
  '-DMATEMENU_I_KNOW_THIS_IS_UNSTABLE',
  '-DPREFIX="@0@"'.format(prefix),
  '-DBINDIR="@0@"'.format(bindir),
  '-DLIBDIR="@0@"'.format(libdir),
  '-DLIBEXECDIR="@0@"'.format(libexecdir),
  '-DDATADIR="@0@"'.format(datadir),
  '-DSYSCONFDIR="@0@"'.format(sysconfdir),
  '-DMATELOCALEDIR="@0@"'.format(localedir),
  '-DPKGLIBEXECDIR="@0@"'.format(pkglibexecdir),
  '-DSAVERDIR="@0@"'.format(pkglibexecdir),
  '-DTHEMESDIR="@0@"'.format(privdatadir / 'themes'),
  '-DGTKBUILDERDIR="@0@"'.format(privdatadir),
  '-DMATE_SCREENSAVER_GL_HELPER_PATH="@0@"'.format(libexecdir / 'mate-screensaver-gl-helper'),
  '-DPAM_SERVICE_NAME="mate-screensaver"',
]

# Configuration Data for config.h
conf = configuration_data()
conf.set_quoted('VERSION', meson.project_version())
conf.set_quoted('GETTEXT_PACKAGE', meson.project_name())
conf.set_quoted('PACKAGE_NAME', meson.project_name())
conf.set_quoted('PACKAGE_VERSION', meson.project_version())
conf.set_quoted('PACKAGE_STRING', '@0@ @1@'.format(meson.project_name(), meson.project_version()))
conf.set('ENABLE_NLS', 1)

# Configuration Data for file substitutions
conf_data = configuration_data()
conf_data.set('VERSION', meson.project_version())
conf_data.set('GETTEXT_PACKAGE', meson.project_name())
conf_data.set('prefix', prefix)
conf_data.set('exec_prefix', prefix)
conf_data.set('libdir', libdir)
conf_data.set('libexecdir', libexecdir)
conf_data.set('includedir', prefix / get_option('includedir'))
conf_data.set('datarootdir', datadir)
conf_data.set('datadir', datadir)
conf_data.set('privlibexecdir', pkglibexecdir)
conf_data.set('privdatadir', privdatadir)
conf_data.set('themesdir', themesdir)
conf_data.set('EXPANDED_BINDIR', bindir)

# Header Checks
headers = [
  'unistd.h',
  'crypt.h',
  'sys/select.h',
  'dlfcn.h',
]
foreach h : headers
  if cc.has_header(h)
    conf.set('HAVE_' + h.underscorify().to_upper(), 1)
  endif
endforeach

# Function Checks
functions = [
  'select',
  'fcntl',
  'uname',
  'nice',
  'setpriority',
  'getcwd',
  'getwd',
  'putenv',
  'sbrk',
  'sigaction',
  'syslog',
  'realpath',
  'setrlimit',
  'getresuid',
  'setresuid',
  'setenv',
  'unsetenv',
  'clearenv',
]
foreach f : functions
  if cc.has_function(f)
    conf.set('HAVE_' + f.underscorify().to_upper(), 1)
  endif
endforeach

# Base Dependencies
glib_dep = dependency('glib-2.0', version: '>= 2.58.0')
gio_dep = dependency('gio-2.0', version: '>= 2.58.0')
gobject_dep = dependency('gobject-2.0', version: '>= 2.58.0')
gthread_dep = dependency('gthread-2.0')
gtk_dep = dependency('gtk+-3.0', version: '>= 3.22.0')
dbus_glib_dep = dependency('dbus-glib-1', version: '>= 0.30')
x11_dep = dependency('x11', version: '>= 1.0')
xext_dep = dependency('xext')
xscrnsaver_dep = dependency('xscrnsaver')
mate_desktop_dep = dependency('mate-desktop-2.0', version: '>= 1.17.0')
mate_menu_dep = dependency('libmate-menu', version: '>= 1.21.0')
sm_dep = dependency('sm')
ice_dep = dependency('ice')
libm = cc.find_library('m', required: false)

# Extra libraries for saver
saver_libs_dep = [x11_dep, xext_dep, xscrnsaver_dep, sm_dep, ice_dep]

# Extra dependencies
libnotify_dep = dependency('libnotify', version: '> 0.7.0', required: get_option('libnotify'))
if libnotify_dep.found()
  conf.set('WITH_LIBNOTIFY', 1)
endif

libmatekbdui_dep = dependency('libmatekbdui', version: '>= 1.17.0', required: get_option('kbd-layout-indicator'))
if libmatekbdui_dep.found()
  conf.set('WITH_KBD_LAYOUT_INDICATOR', 1)
endif

# Math lib and Shape Extension
if cc.has_header('X11/extensions/shape.h', dependencies: x11_dep)
  conf.set('HAVE_SHAPE_EXT', 1)
endif

# MIT Saver Extension
have_mit = false
if get_option('mit-ext')
  if cc.has_header('X11/extensions/scrnsaver.h', dependencies: x11_dep)
    xss_lib = cc.find_library('Xss', required: false)
    if xss_lib.found() and cc.has_function('XScreenSaverRegister', dependencies: [x11_dep, xss_lib])
      have_mit = true
      saver_libs_dep += xss_lib
    endif
  endif
endif
if have_mit
  conf.set('HAVE_MIT_SAVER_EXTENSION', 1)
endif

# XF86VMODE Gamma Extension
have_xf86gamma = false
have_xf86gamma_ramp = false
if get_option('xf86gamma-ext')
  if cc.has_header('X11/extensions/xf86vmode.h', dependencies: x11_dep)
    xf86vm_lib = cc.find_library('Xxf86vm', required: false)
    if xf86vm_lib.found()
      if cc.has_function('XF86VidModeSetGamma', dependencies: [x11_dep, xf86vm_lib])
        have_xf86gamma = true
        saver_libs_dep += xf86vm_lib
      endif
      if cc.has_function('XF86VidModeSetGammaRamp', dependencies: [x11_dep, xf86vm_lib])
        have_xf86gamma_ramp = true
      endif
    endif
  endif
endif
if have_xf86gamma
  conf.set('HAVE_XF86VMODE_GAMMA', 1)
endif
if have_xf86gamma_ramp
  conf.set('HAVE_XF86VMODE_GAMMA_RAMP', 1)
endif

# Locking support
enable_locking = get_option('locking')
if not enable_locking
  conf.set('NO_LOCKING', 1)
endif

# Shadow Passwords
auth_deps = []
crypt_dep = cc.find_library('crypt', required: false)
if crypt_dep.found()
  auth_deps += crypt_dep
endif

have_shadow = false
if get_option('shadow') and enable_locking
  if cc.has_header('shadow.h') and cc.has_function('getspnam', dependencies: crypt_dep)
    have_shadow = true
    conf.set('HAVE_SHADOW_PASSWD', 1)
  endif
endif

# PAM and Authentication Scheme
auth_scheme = get_option('authentication-scheme')
passwd_helper = get_option('passwd-helper')

have_pam = false
pam_dep = []
if get_option('pam') and enable_locking
  pam_lib = cc.find_library('pam', required: get_option('pam'))
  if pam_lib.found()
    have_pam = true
    pam_dep = pam_lib
  endif
endif

have_bsdauth = false
if host_machine.system() == 'openbsd' and enable_locking
  have_bsdauth = cc.links('''
    #include <stdlib.h>
    #include <unistd.h>
    #include <sys/types.h>
    #include <bsd_auth.h>
    int main() {
      return auth_userokay("x", 0, "x", "x");
    }
  ''', name: 'BSD Authentication')
endif

have_passwd_helper = passwd_helper != ''
builtin_helper = false

if auth_scheme == 'auto'
  if have_pam
    auth_scheme = 'pam'
  elif have_passwd_helper
    auth_scheme = 'helper'
  elif have_bsdauth
    auth_scheme = 'bsdauth'
  else
    auth_scheme = 'pwent'
  endif
elif auth_scheme == 'helper' and not have_passwd_helper
  builtin_helper = true
  have_passwd_helper = true
endif

if auth_scheme == 'pam'
  conf.set('HAVE_PAM', 1)
  auth_deps += pam_dep
  pam_strerror_two = cc.compiles('''
    #include <security/pam_appl.h>
    int main() {
      pam_handle_t *pamh = 0;
      char *s = pam_strerror(pamh, PAM_SUCCESS);
      return 0;
    }
  ''', dependencies: pam_dep, name: 'pam_strerror takes 2 arguments')
  if pam_strerror_two
    conf.set('PAM_STRERROR_TWO_ARGS', 1)
  endif
elif auth_scheme == 'helper'
  conf.set('HAVE_PASSWD_HELPER', 1)
elif auth_scheme == 'bsdauth'
  conf.set('HAVE_BSDAUTH', 1)
endif

if builtin_helper
  conf.set_quoted('PASSWD_HELPER_PROGRAM', pkglibexecdir / 'mate-screensaver-pam-helper')
elif have_passwd_helper and passwd_helper != 'builtin'
  conf.set_quoted('PASSWD_HELPER_PROGRAM', passwd_helper)
endif

# PAM prefix
pam_prefix = get_option('pam-prefix')
if pam_prefix == ''
  pam_prefix = sysconfdir
endif

# ConsoleKit
if get_option('console-kit')
  conf.set('WITH_CONSOLE_KIT', 1)
endif

# Systemd & elogind
systemd_deps = []
use_systemd = false
use_elogind = false

if not get_option('elogind').disabled()
  elogind_dep = dependency('libelogind', required: get_option('elogind'))
  if elogind_dep.found()
    use_elogind = true
    systemd_deps += elogind_dep
    conf.set('WITH_ELOGIND', 1)
    conf.set('WITH_SYSTEMD', 1)
  endif
endif

if not use_elogind and not get_option('systemd').disabled()
  systemd_dep = dependency('libsystemd', required: false)
  if not systemd_dep.found()
    systemd_dep = dependency('libsystemd-login', required: get_option('systemd'))
  endif
  if systemd_dep.found()
    use_systemd = true
    systemd_deps += systemd_dep
    conf.set('WITH_SYSTEMD', 1)
  endif
endif

# GL
have_libgl = false
libgl_dep = []
if not get_option('libgl').disabled()
  gl_dep = dependency('gl', required: get_option('libgl'))
  if gl_dep.found()
    have_libgl = true
    libgl_dep = gl_dep
    conf.set('HAVE_LIBGL', 1)
  endif
endif

# Xscreensaver configuration directories
xscreensaver_dir = get_option('xscreensaverdir')
if xscreensaver_dir != ''
  conf.set_quoted('XSCREENSAVER_CONFIG_DIR', xscreensaver_dir)
endif

xscreensaver_hack_dir = get_option('xscreensaverhackdir')
if xscreensaver_hack_dir != ''
  conf.set_quoted('XSCREENSAVER_HACK_DIR', xscreensaver_hack_dir)
endif

# DBus services dir
dbus_dep = dependency('dbus-1', required: false)
if dbus_dep.found()
  dbus_session_service_dir = dbus_dep.get_variable(pkgconfig: 'session_bus_services_dir', default_value: datadir / 'dbus-1' / 'services')
else
  dbus_session_service_dir = datadir / 'dbus-1' / 'services'
endif

# Configure files
configure_file(output: 'config.h', configuration: conf)

# Dependency Groups
mate_screensaver_dep = [
  dependency('gio-2.0', version: '>= 2.58.0'),
  dependency('glib-2.0', version: '>= 2.58.0'),
  dependency('gtk+-3.0', version: '>= 3.22.0'),
  dependency('dbus-glib-1', version: '>= 0.30'),
  dependency('mate-desktop-2.0', version: '>= 1.17.0'),
  dependency('libmate-menu', version: '>= 1.21.0'),
]

mate_screensaver_command_dep = [
  dependency('gobject-2.0', version: '>= 2.58.0'),
  dependency('dbus-glib-1', version: '>= 0.30'),
]

mate_screensaver_capplet_dep = [
  dependency('gio-2.0', version: '>= 2.58.0'),
  dependency('gtk+-3.0', version: '>= 3.22.0'),
  dependency('mate-desktop-2.0', version: '>= 1.17.0'),
  dependency('libmate-menu', version: '>= 1.21.0'),
]

mate_screensaver_dialog_dep = [
  dependency('gio-2.0', version: '>= 2.58.0'),
  dependency('gthread-2.0'),
  dependency('gtk+-3.0', version: '>= 3.22.0'),
  dependency('mate-desktop-2.0', version: '>= 1.17.0'),
]

mate_screensaver_saver_dep = [
  dependency('gthread-2.0'),
  dependency('gtk+-3.0', version: '>= 3.22.0'),
  dependency('gobject-2.0', version: '>= 2.58.0'),
]

# Set options variables for subdirs
po_dir = meson.current_source_dir() / 'po'
docdir = get_option('doc-dir')
if docdir == ''
  docdir = datadir / 'doc' / 'mate-screensaver'
endif

# Subdirs
subdir('po')
subdir('helper')
subdir('src')
subdir('savers')
subdir('data')
subdir('doc')

gnome.post_install(
  glib_compile_schemas: true,
  update_desktop_database: true,
)
