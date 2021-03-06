project('bindinator', ['cs'], version: '0.1', meson_version: '>0.46', license: 'LGPL')

dep = dependency('gobject-introspection-1.0', required: false)

found = false
cdata = configuration_data()

gtk_sharp = subproject('gtk-sharp')
gapi_fixup = gtk_sharp.get_variable('gapi_fixup')

xsltproc = find_program('xsltproc', required: false)
if not xsltproc.found()
    warning('Bindinator is not usable because xsltproc was not found')
endif

pkgconfig = find_program('pkg-config', required: false)
if not pkgconfig.found()
    warning('Bindinator is not usable because pkgconfig was not found')
endif

if xsltproc.found() and pkgconfig.found()
    found = true
    if dep.found()
        cdata.set_quoted('GIRDIR', dep.get_pkgconfig_variable('girdir'))
    else
        cdata.set_quoted('GIRDIR', join_paths(get_option('prefix'), get_option('datadir'), 'gir-1.0'))
    endif
    cdata.set_quoted('XSLTPROC', xsltproc.path())

    cdata.set_quoted('PREPROCESSXSLT', join_paths(meson.current_source_dir(), 'bindinate', 'preprocess.xslt'))
    cdata.set_quoted('GIR2GAPIXSLT', join_paths(meson.current_build_dir(), 'gir2gapi.xslt'))
    cdata.set_quoted('MERGEXSLT', join_paths(meson.current_source_dir(), 'bindinate', 'merge.xslt'))
    cdata.set_quoted('UNIX2DOS', 'UNKNOWN')
    cdata.set_quoted('UUIDGEN', 'UNKNOWN')
    cdata.set_quoted('XMLLINT', 'UNKNOWN')

    cdata.set_quoted('PKG_CONFIG', pkgconfig.path())
    cdata.set_quoted('prefix', get_option('prefix'))
    cdata.set_quoted('GAPIFIXUP', gapi_fixup.full_path())

    configure_file(output : 'bindinate', input: 'bindinate/bindinate.in', configuration : cdata)

    cdata = configuration_data()
    cdata.set('GIRDIR', dep.get_pkgconfig_variable('girdir') + '/')
    configure_file(output : 'gir2gapi.xslt', input: 'bindinate/gir2gapi.xslt.in', configuration : cdata)

    bindinate = find_program(join_paths(meson.current_build_dir(), 'bindinate'))
endif