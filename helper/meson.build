libhelper_proto = []

if have_passwd_helper
  libhelper_proto = static_library('helper-proto',
    sources: ['helper_proto.c', 'helper_proto.h'],
    dependencies: [glib_dep],
    include_directories: [include_directories('../src'), root_inc]
  )

  if builtin_helper
    executable('mate-screensaver-pam-helper',
      sources: ['pam-helper.c'],
      dependencies: [glib_dep] + auth_deps,
      link_with: libhelper_proto,
      include_directories: [include_directories('../src'), root_inc],
      install: true,
      install_dir: pkglibexecdir
    )
  endif
endif
