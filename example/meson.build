examples = [ 'passthrough', 'passthrough_fh',
             'hello', 'hello_ll', 'hello_ll_uds',
             'printcap', 'ioctl_client', 'poll_client',
             'ioctl', 'cuse', 'cuse_client' ]

if not platform.endswith('bsd') and platform != 'dragonfly'
    examples += 'passthrough_ll'

    # According to Conrad Meyer <cem@freebsd.org>, FreeBSD doesn't
    # support mounting files, This is enforced in vfs_domount_first()
    # with the v_type != VDIR check.
    examples += [ 'null' ]
endif

threaded_examples = [ 'notify_inval_inode',
                      'invalidate_path',
                      'notify_store_retrieve',
                      'notify_inval_entry',
                      'poll' ]

fuse_bpf_examples = [ 'fuse_daemon' ]

bpf_examples = [ 'fd_bpf' ]

foreach ex : examples
    executable(ex, ex + '.c',
               dependencies: [ libfuse_dep ],
               install: false)
endforeach


foreach ex : threaded_examples
    executable(ex, ex + '.c',
               dependencies: [ thread_dep, libfuse_dep ],
               install: false)
endforeach

libelf_dep = dependency('libelf')
foreach ex : fuse_bpf_examples
    executable(ex, [ 'fuse_bpf/' + ex + '.c', 'fuse_bpf/bpf_loader.c' ],
               dependencies: [ libfuse_dep, libelf_dep ],
               install: false)
endforeach

clang = find_program('clang')

infile = 'fuse_bpf/fd_bpf.c'
outfile = 'fd_bpf.ir'

infile2 = custom_target('targetname',
  output : outfile,
  input : infile,
  command : [clang, '-emit-llvm', '-O2', '-c', '-g', '@INPUT@', '-o', '@OUTPUT@'])

llc = find_program('llc')

outfile2 = 'fd_bpf.bpf'

mytarget2 = custom_target('targetname2',
  output : outfile2,
  input : infile2,
  command : [llc, '-march=bpf', '-filetype=obj', '-o', '@OUTPUT@', '@INPUT@'],
  install : true,
  install_dir : 'subdir')

foreach ex : bpf_examples
endforeach

if not platform.endswith('bsd') and platform != 'dragonfly' and add_languages('cpp', required : false)
    executable('passthrough_hp', 'passthrough_hp.cc',
               dependencies: [ thread_dep, libfuse_dep ],
               install: false)
endif

# TODO: Link passthrough_fh with ulockmgr if available
