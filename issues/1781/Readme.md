# openEuler-25.03-GreatSQL测试报告

## 版本说明

系统版本:

```
OS: openEuler 25.03 riscv64
Host: riscv-virtio,qemu
Kernel: Linux 6.6.0-72.6.0.56.oe2503.riscv64
Uptime: 2 days, 23 hours, 25 mins
Packages: 465 (rpm)
Shell: fish 3.7.1
Display (QEMU Monitor): 1280x800 @ 75 Hz in 15"
Cursor: Adwaita
Terminal: /dev/pts/4
CPU: rv64gch (8)
GPU: RedHat Device 1050 (VGA compatible)
```



GreatSQL 版本:

```
Mega:25.03:Everything/greatsql/

root@localhost ~/M/greatsql# ls
boost_1_77_0.tar.bz2 greatsql-802516-mtr-passed.md greatsql-8.0.32-26.tar.xz    mysql-5.7-sharedlib-rename.patch
```



关键词：GreatSQL

摘要：按照 GreatSQL 8.0.32-26 测试用例要求，部署 openEuler 25.03 测试镜像环境，对 GreatSQL 的源码编译、RPM安装、二进制包安装、主要功能进行测试。





## 测试结论概述



在 Qemu RISCV64 中启动 openEuler 25.03测试镜像，在此基础上进行 GreatSQL 8.0.32-26 测试，GreatSQL 包从 `Mega:25.03:Everything/greatsql` 仓库中拉取.



`osc build standard_riscv64 riscv64`编译, greatsql 未能够成功编译, 编译报错原因主要为 `clang: error: unknown argument: '-fgcc-compatible'`







测试共执行 139 个测试项，主要涵盖了 GreatSQL 源码编译、RPM安装、二进制包安装、MGR增强、Binlog读取限速、Clone复制数据时自动最新节点、并行LOAD DATA、异步删除大表、非阻塞式DDL、NUMA亲和性优化、Oracle兼容、Clone备份加密、Clone增量备份、Clone压缩备份、审计、数据脱敏、最后登录信息等主要功能特性等方面，主要功能均通过测试，无风险，整体核心功能稳定正常。





## 测试执行



### 编译报错



编译报错如下:

```
[   77s] ----- building greatsql.spec (user abuild)
[   77s] -----------------------------------------------------------------
[   77s] -----------------------------------------------------------------
[   81s] + exec rpmbuild -ba --define '_srcdefattr (-,root,root)' --nosignature --undefine _enable_debug_packages /home/abuild/rpmbuild/SOURCES/greatsql.spec
[   81s] warning: extra tokens at the end of %endif directive in line 748:  %endif # systemd
[   81s] 
[   81s] warning: extra tokens at the end of %endif directive in line 758:  %endif # systemd
[   81s] 
[   81s] warning: extra tokens at the end of %endif directive in line 768:  %endif # systemd
[   81s] 
[   81s] warning: Macro expanded in comment on line 784: %{?systemd}
[   81s] 
[   81s] warning: Macro expanded in comment on line 785: %{_mandir}/man1/mysqld_multi.1*
[   81s] 
[   81s] warning: Macro expanded in comment on line 786: %{_mandir}/man1/mysqld_safe.1*
[   81s] 
[   81s] warning: Macro expanded in comment on line 788: %{_mandir}/man1/mysqld_multi.1*
[   81s] 
[   81s] warning: Macro expanded in comment on line 789: %{_mandir}/man1/mysqld_safe.1*
[   81s] 
[   81s] warning: Macro expanded in comment on line 795: %{_mandir}/man1/mysql.server.1*
[   81s] 
[   81s] warning: Macro expanded in comment on line 965: %{_datadir}/mysql-*/audit_log_filter_linux_install.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 966: %{_libdir}/mysql/plugin/authentication_pam.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 967: %{_libdir}/mysql/plugin/authentication_ldap_sasl.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 968: %{_libdir}/mysql/plugin/authentication_ldap_simple.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 969: %{_libdir}/mysql/plugin/keyring_okv.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 970: %{_libdir}/mysql/plugin/keyring_encrypted_file.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 971: %{_libdir}/mysql/plugin/mysql_clone.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 972: %{_libdir}/mysql/plugin/thread_pool.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 973: %{_libdir}/mysql/plugin/openssl_udf.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 974: %{_libdir}/mysql/plugin/firewall.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 975: %{_datadir}/mysql-*/linux_install_firewall.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 978: %{_libdir}/mysql/plugin/scalability_metrics.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 979: %{_libdir}/mysql/plugin/debug/scalability_metrics.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 992: %{_libdir}/mysql/plugin/query_response_time.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 993: %{_libdir}/mysql/plugin/debug/query_response_time.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 997: %{_datadir}/greatsql/fill_help_tables.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 998: %{_datadir}/greatsql/mysql_sys_schema.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 999: %{_datadir}/greatsql/mysql_system_tables.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1000: %{_datadir}/greatsql/mysql_system_tables_data.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1001: %{_datadir}/greatsql/mysql_test_data_timezone.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1003: %{_datadir}/greatsql/mysql_security_commands.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1052: %{_datadir}/greatsql/mysql_system_users.sql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1105: %{?compatlib}
[   81s] 
[   81s] warning: Macro expanded in comment on line 1108: %{?license_files_server}
[   81s] 
[   81s] warning: Macro expanded in comment on line 1109: %{_libdir}/mysql
[   81s] 
[   81s] warning: Macro expanded in comment on line 1110: %{_sysconfdir}/ld.so.conf.d/mysql-%{_arch}.conf
[   81s] 
[   81s] warning: Macro expanded in comment on line 1111: %{_libdir}/mysql/libmysqlclient.so.%{compatlib}.*
[   81s] 
[   81s] warning: Macro expanded in comment on line 1112: %{_libdir}/mysql/libmysqlclient_r.so.%{compatlib}.*
[   81s] 
[   81s] warning: Macro expanded in comment on line 1270: %{?tokudb}
[   81s] 
[   81s] warning: Macro expanded in comment on line 1273: %{_bindir}/tokuftdump
[   81s] 
[   81s] warning: Macro expanded in comment on line 1274: %{_libdir}/mysql/plugin/ha_tokudb.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 1275: %{_libdir}/mysql/plugin/debug/ha_tokudb.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 1276: %{_bindir}/tokuft_logprint
[   81s] 
[   81s] warning: Macro expanded in comment on line 1277: %{_libdir}/mysql/plugin/tokudb_backup.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 1278: %{_libdir}/mysql/plugin/debug/tokudb_backup.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 1279: %{_libdir}/mysql/libHotBackup.so
[   81s] 
[   81s] warning: Macro expanded in comment on line 1280: %{_includedir}/backup.h
[   81s] 
[   81s] setting SOURCE_DATE_EPOCH=1733616000
[   81s] Executing(%prep): /bin/sh -e /var/tmp/rpm-tmp.TcA7iR
[   81s] + umask 022
[   81s] + cd /home/abuild/rpmbuild/BUILD
[   81s] + cd /home/abuild/rpmbuild/BUILD
[   81s] + rm -rf greatsql-8.0.32-26
[   81s] + /usr/bin/mkdir -p greatsql-8.0.32-26
[   81s] + cd greatsql-8.0.32-26
[   81s] + /usr/lib/rpm/rpmuncompress -x /home/abuild/rpmbuild/SOURCES/greatsql-8.0.32-26.tar.xz
[  164s] + STATUS=0
[  164s] + '[' 0 -ne 0 ']'
[  164s] + /usr/lib/rpm/rpmuncompress -x /home/abuild/rpmbuild/SOURCES/boost_1_77_0.tar.bz2
[  224s] + STATUS=0
[  224s] + '[' 0 -ne 0 ']'
[  224s] + /usr/bin/chmod -Rf a+rX,u+w,g-w,o-w .
[  231s] + pushd greatsql-8.0.32-26
[  231s] ~/rpmbuild/BUILD/greatsql-8.0.32-26/greatsql-8.0.32-26 ~/rpmbuild/BUILD/greatsql-8.0.32-26
[  231s] + echo 'Patch #0 (mysql-5.7-sharedlib-rename.patch):'
[  231s] Patch #0 (mysql-5.7-sharedlib-rename.patch):
[  231s] + /usr/bin/patch --no-backup-if-mismatch -f -p0 --fuzz=0
[  231s] patching file client/base/CMakeLists.txt
[  231s] patching file client/CMakeLists.txt
[  231s] Hunk #1 succeeded at 50 (offset 1 line).
[  231s] Hunk #2 succeeded at 74 (offset 1 line).
[  231s] Hunk #3 succeeded at 84 (offset 1 line).
[  231s] Hunk #4 succeeded at 250 (offset 1 line).
[  231s] Hunk #5 succeeded at 292 (offset 1 line).
[  231s] patching file client/migrate_keyring/CMakeLists.txt
[  231s] patching file include/mysql.h
[  231s] patching file libmysql/CMakeLists.txt
[  231s] Hunk #1 succeeded at 286 (offset 3 lines).
[  231s] Hunk #2 succeeded at 329 (offset 3 lines).
[  231s] Hunk #3 succeeded at 356 (offset 3 lines).
[  231s] Hunk #4 succeeded at 371 (offset 3 lines).
[  231s] patching file libmysql/libmysql.ver.in
[  231s] patching file plugin/percona-pam-for-mysql/CMakeLists.txt
[  231s] patching file plugin/x/tests/driver/CMakeLists.txt
[  231s] Hunk #1 succeeded at 43 (offset 1 line).
[  231s] patching file router/src/router/src/CMakeLists.txt
[  231s] Hunk #1 succeeded at 60 (offset -11 lines).
[  231s] patching file router/tests/fuzzers/CMakeLists.txt
[  231s] patching file scripts/CMakeLists.txt
[  231s] Hunk #1 succeeded at 319 (offset 17 lines).
[  231s] Hunk #2 succeeded at 361 (offset 24 lines).
[  231s] patching file storage/ndb/test/run-test/CMakeLists.txt
[  231s] patching file storage/ndb/tools/CMakeLists.txt
[  231s] Hunk #1 succeeded at 244 (offset 14 lines).
[  231s] patching file testclients/CMakeLists.txt
[  231s] patching file unittest/gunit/group_replication/CMakeLists.txt
[  231s] Hunk #1 succeeded at 51 (offset 4 lines).
[  231s] patching file unittest/gunit/xplugin/xcl/CMakeLists.txt
[  231s] Hunk #1 succeeded at 68 (offset -9 lines).
[  231s] patching file unittest/gunit/xplugin/xpl/CMakeLists.txt
[  231s] Hunk #1 succeeded at 79 (offset -36 lines).
[  231s] patching file libmysql/authentication_kerberos/CMakeLists.txt
[  231s] patching file unittest/gunit/changestreams/CMakeLists.txt
[  231s] patching file router/src/router/tests/CMakeLists.txt
[  231s] Hunk #1 succeeded at 128 (offset -2 lines).
[  231s] Hunk #2 succeeded at 144 (offset -2 lines).
[  231s] Hunk #3 succeeded at 166 (offset -2 lines).
[  231s] Hunk #4 succeeded at 188 (offset -2 lines).
[  231s] + echo 'Patch #1 (add-riscv64-support.patch):'
[  231s] Patch #1 (add-riscv64-support.patch):
[  231s] + /usr/bin/patch --no-backup-if-mismatch -f -p1 --fuzz=0
[  231s] patching file 'GreatSQL 8.0.32-25在openEuler riscv64中完成MTR回归测试.md'
[  231s] patching file storage/ndb/include/portlib/mt-asm.h
[  231s] Hunk #1 succeeded at 84 (offset 1 line).
[  231s] patching file '在OpenEuler RISC-V环境下源码编译安装GreatSQL.md'
[  231s] patching file storage/ndb/include/portlib/mt-asm.h
[  231s] Hunk #1 succeeded at 95 (offset 1 line).
[  231s] patching file CMakeLists.txt
[  231s] Hunk #1 succeeded at 722 (offset -12 lines).
[  231s] + cp /home/abuild/rpmbuild/SOURCES/mysqld.cnf scripts
[  231s] + RPM_EC=0
[  231s] ++ jobs -p
[  231s] + exit 0
[  232s] Executing(%build): /bin/sh -e /var/tmp/rpm-tmp.ZzbP1E
[  232s] + umask 022
[  232s] + cd /home/abuild/rpmbuild/BUILD
[  232s] + CC=clang
[  232s] + export CC
[  232s] + CXX=clang++
[  232s] + export CXX
[  232s] + CCC=clang++
[  232s] + export CCC
[  232s] + CC=clang
[  232s] + export CC
[  232s] + CXX=clang++
[  232s] + export CXX
[  232s] + CCC=clang++
[  232s] + export CCC
[  232s] + CFLAGS='-O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection -fsigned-char -march=rv64gcv_zicboz_zicsr_zihintpause_zihpm_zba_zbb_zbs -fPIC -Wl,-z,noexecstack -Wno-unused-command-line-argument'
[  232s] + export CFLAGS
[  232s] + CXXFLAGS='-O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection -fsigned-char -fexceptions -march=rv64gcv_zicboz_zicsr_zihintpause_zihpm_zba_zbb_zbs -fPIC -Wl,-z,noexecstack -Wno-unused-command-line-argument'
[  232s] + export CXXFLAGS
[  232s] + FFLAGS='-O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection -fexceptions -I/usr/lib64/gfortran/modules'
[  232s] + export FFLAGS
[  232s] + FCFLAGS='-O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection -fexceptions -I/usr/lib64/gfortran/modules'
[  232s] + export FCFLAGS
[  232s] + LDFLAGS='-Wl,-z,relro -Wl,--as-needed  -Wl,-z,now '
[  232s] + export LDFLAGS
[  232s] + cd greatsql-8.0.32-26
[  232s] + mkdir debug
[  232s] + cd debug
[  232s] ++ echo '-O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection'
[  232s] ++ sed -e 's/-O2 / /' -e 's/-Wp,-D_FORTIFY_SOURCE=2/ -Wno-missing-field-initializers -Wno-error /'
[  232s] + optflags=' -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection'
[  232s] ++ echo -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection
[  232s] ++ sed -e 's/-specs=\/usr\/lib\/rpm\/redhat\/redhat-hardened-cc1 -specs=\/usr\/lib\/rpm\/redhat\/redhat-annobin-cc1/ /'
[  232s] + optflags='-g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection'
[  232s] + cmake ../greatsql-8.0.32-26 -DBUILD_CONFIG=mysql_release -DINSTALL_LAYOUT=RPM -DCMAKE_BUILD_TYPE=Debug -DWITH_BOOST=.. '-DCMAKE_C_FLAGS=-g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection' '-DCMAKE_CXX_FLAGS=-g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection' -DWITH_SYSTEMD=1 -DWITH_INNODB_MEMCACHED=1 -DINSTALL_LIBDIR=lib64/mysql -DINSTALL_PLUGINDIR=lib64/mysql/plugin -DMYSQL_UNIX_ADDR=/var/lib/mysql/mysql.sock -DINSTALL_MYSQLSHAREDIR=share/greatsql -DINSTALL_SUPPORTFILESDIR=share/greatsql -DFEATURE_SET=community -DWITH_AUTHENTICATION_LDAP=OFF -DWITH_PAM=1 -DWITH_ROCKSDB=OFF -DWITH_UNIT_TESTS=OFF -DALLOW_NO_SSE42=ON -DROCKSDB_DISABLE_AVX2=1 -DROCKSDB_DISABLE_MARCH_NATIVE=1 -DGROUP_REPLICATION_WITH_ROCKSDB=OFF -DWITH_TOKUDB=0 -DWITH_TOKUDB_BACKUP=OFF -DWITH_INNODB_MEMCACHED=1 -DMYSQL_MAINTAINER_MODE=OFF -DFORCE_INSOURCE_BUILD=1 -DWITH_NUMA=ON -DWITH_LDAP=system -DWITH_SYSTEM_LIBS=ON -DWITH_PROTOBUF=bundled -DWITH_RAPIDJSON=bundled -DWITH_ICU=bundled -DWITH_LZ4=bundled -DWITH_ZLIB=bundled -DWITH_ZSTD=bundled -DWITH_READLINE=system -DWITH_LIBEVENT=bundled -DWITH_KEYRING_VAULT=ON -DWITH_FIDO=bundled -DWITHOUT_RAPID_SECONDARY_STORAGE_ENGINE=1 -DWITH_SSL=system '-DCOMPILATION_COMMENT=GreatSQL - Debug (GPL), Release 26, Revision a68b3034c3d' -DWITHOUT_TOKUDB=1 -DWITH_ROCKSDB=0
[  232s] -- Running cmake version 3.27.9
[  232s] -- Found Git: /usr/bin/git (found version "2.43.0") 
[  233s] -- CMAKE_MODULE_PATH is /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/greatsql-8.0.32-26/cmake
[  233s] -- MySQL 8.0.32-26
[  236s] -- The C compiler identification is Clang 19.1.7
[  239s] -- The CXX compiler identification is Clang 19.1.7
[  239s] -- Detecting C compiler ABI info
[  240s] -- Detecting C compiler ABI info - failed
[  240s] -- Check for working C compiler: /usr/bin/clang
[  241s] -- Check for working C compiler: /usr/bin/clang - broken
[  241s] CMake Error at /usr/share/cmake/Modules/CMakeTestCCompiler.cmake:67 (message):
[  241s]   The C compiler
[  241s] 
[  241s]     "/usr/bin/clang"
[  241s] 
[  241s]   is not able to compile a simple test program.
[  241s] 
[  241s]   It fails with the following output:
[  241s] 
[  241s]     Change Dir: '/home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/debug/CMakeFiles/CMakeScratch/TryCompile-PnNYfD'
[  241s]     
[  241s]     Run Build Command(s): /usr/bin/cmake -E env VERBOSE=1 /usr/bin/gmake -f Makefile cmTC_4c8f8/fast
[  241s]     /usr/bin/gmake  -f CMakeFiles/cmTC_4c8f8.dir/build.make CMakeFiles/cmTC_4c8f8.dir/build
[  241s]     gmake[1]: Entering directory '/home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/debug/CMakeFiles/CMakeScratch/TryCompile-PnNYfD'
[  241s]     Building C object CMakeFiles/cmTC_4c8f8.dir/testCCompiler.c.o
[  241s]     /usr/bin/clang   -g -grecord-gcc-switches -pipe -fstack-protector-strong -fgcc-compatible -Wall -Werror=format-security -Wp,-U_FORTIFY_SOURCE,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS --config /usr/lib/rpm/generic-hardened-clang.cfg -fasynchronous-unwind-tables -fstack-clash-protection  -MD -MT CMakeFiles/cmTC_4c8f8.dir/testCCompiler.c.o -MF CMakeFiles/cmTC_4c8f8.dir/testCCompiler.c.o.d -o CMakeFiles/cmTC_4c8f8.dir/testCCompiler.c.o -c /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/debug/CMakeFiles/CMakeScratch/TryCompile-PnNYfD/testCCompiler.c
[  241s]     clang: error: unknown argument: '-fgcc-compatible'
[  241s]     gmake[1]: *** [CMakeFiles/cmTC_4c8f8.dir/build.make:79: CMakeFiles/cmTC_4c8f8.dir/testCCompiler.c.o] Error 1
[  241s]     gmake[1]: Leaving directory '/home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/debug/CMakeFiles/CMakeScratch/TryCompile-PnNYfD'
[  241s]     gmake: *** [Makefile:127: cmTC_4c8f8/fast] Error 2
[  241s]     
[  241s]     
[  241s] 
[  241s]   
[  241s] 
[  241s]   CMake will not be able to correctly generate this project.
[  241s] Call Stack (most recent call first):
[  241s]   CMakeLists.txt:596 (PROJECT)
[  241s] 
[  241s] 
[  241s] -- Configuring incomplete, errors occurred!
[  241s] error: Bad exit status from /var/tmp/rpm-tmp.ZzbP1E (%build)
[  241s] 
[  241s] RPM build warnings:
[  241s]     extra tokens at the end of %endif directive in line 748:  %endif # systemd
[  241s] 
[  241s]     extra tokens at the end of %endif directive in line 758:  %endif # systemd
[  241s] 
[  241s]     extra tokens at the end of %endif directive in line 768:  %endif # systemd
[  241s] 
[  241s]     Macro expanded in comment on line 784: %{?systemd}
[  241s] 
[  241s]     Macro expanded in comment on line 785: %{_mandir}/man1/mysqld_multi.1*
[  241s] 
[  241s]     Macro expanded in comment on line 786: %{_mandir}/man1/mysqld_safe.1*
[  241s] 
[  241s]     Macro expanded in comment on line 788: %{_mandir}/man1/mysqld_multi.1*
[  241s] 
[  241s]     Macro expanded in comment on line 789: %{_mandir}/man1/mysqld_safe.1*
[  241s] 
[  241s]     Macro expanded in comment on line 795: %{_mandir}/man1/mysql.server.1*
[  241s] 
[  241s]     Macro expanded in comment on line 965: %{_datadir}/mysql-*/audit_log_filter_linux_install.sql
[  241s] 
[  241s]     Macro expanded in comment on line 966: %{_libdir}/mysql/plugin/authentication_pam.so
[  241s] 
[  241s]     Macro expanded in comment on line 967: %{_libdir}/mysql/plugin/authentication_ldap_sasl.so
[  241s] 
[  241s]     Macro expanded in comment on line 968: %{_libdir}/mysql/plugin/authentication_ldap_simple.so
[  241s] 
[  241s]     Macro expanded in comment on line 969: %{_libdir}/mysql/plugin/keyring_okv.so
[  241s] 
[  241s]     Macro expanded in comment on line 970: %{_libdir}/mysql/plugin/keyring_encrypted_file.so
[  241s] 
[  241s]     Macro expanded in comment on line 971: %{_libdir}/mysql/plugin/mysql_clone.so
[  241s] 
[  241s]     Macro expanded in comment on line 972: %{_libdir}/mysql/plugin/thread_pool.so
[  241s] 
[  241s]     Macro expanded in comment on line 973: %{_libdir}/mysql/plugin/openssl_udf.so
[  241s] 
[  241s]     Macro expanded in comment on line 974: %{_libdir}/mysql/plugin/firewall.so
[  241s] 
[  241s]     Macro expanded in comment on line 975: %{_datadir}/mysql-*/linux_install_firewall.sql
[  241s] 
[  241s]     Macro expanded in comment on line 978: %{_libdir}/mysql/plugin/scalability_metrics.so
[  241s] 
[  241s]     Macro expanded in comment on line 979: %{_libdir}/mysql/plugin/debug/scalability_metrics.so
[  241s] 
[  241s]     Macro expanded in comment on line 992: %{_libdir}/mysql/plugin/query_response_time.so
[  241s] 
[  241s]     Macro expanded in comment on line 993: %{_libdir}/mysql/plugin/debug/query_response_time.so
[  241s] 
[  241s]     Macro expanded in comment on line 997: %{_datadir}/greatsql/fill_help_tables.sql
[  241s] 
[  241s]     Macro expanded in comment on line 998: %{_datadir}/greatsql/mysql_sys_schema.sql
[  241s] 
[  241s]     Macro expanded in comment on line 999: %{_datadir}/greatsql/mysql_system_tables.sql
[  241s] 
[  241s]     Macro expanded in comment on line 1000: %{_datadir}/greatsql/mysql_system_tables_data.sql
[  241s] 
[  241s]     Macro expanded in comment on line 1001: %{_datadir}/greatsql/mysql_test_data_timezone.sql
[  241s] 
[  241s]     Macro expanded in comment on line 1003: %{_datadir}/greatsql/mysql_security_commands.sql
[  241s] 
[  241s]     Macro expanded in comment on line 1052: %{_datadir}/greatsql/mysql_system_users.sql
[  241s] 
[  241s]     Macro expanded in comment on line 1105: %{?compatlib}
[  241s] 
[  241s]     Macro expanded in comment on line 1108: %{?license_files_server}
[  241s] 
[  241s]     Macro expanded in comment on line 1109: %{_libdir}/mysql
[  241s] 
[  241s]     Macro expanded in comment on line 1110: %{_sysconfdir}/ld.so.conf.d/mysql-%{_arch}.conf
[  241s] 
[  241s]     Macro expanded in comment on line 1111: %{_libdir}/mysql/libmysqlclient.so.%{compatlib}.*
[  241s] 
[  241s]     Macro expanded in comment on line 1112: %{_libdir}/mysql/libmysqlclient_r.so.%{compatlib}.*
[  241s] 
[  241s]     Macro expanded in comment on line 1270: %{?tokudb}
[  241s] 
[  241s]     Macro expanded in comment on line 1273: %{_bindir}/tokuftdump
[  241s] 
[  241s]     Macro expanded in comment on line 1274: %{_libdir}/mysql/plugin/ha_tokudb.so
[  241s] 
[  241s]     Macro expanded in comment on line 1275: %{_libdir}/mysql/plugin/debug/ha_tokudb.so
[  241s] 
[  241s]     Macro expanded in comment on line 1276: %{_bindir}/tokuft_logprint
[  241s] 
[  241s]     Macro expanded in comment on line 1277: %{_libdir}/mysql/plugin/tokudb_backup.so
[  241s] 
[  241s]     Macro expanded in comment on line 1278: %{_libdir}/mysql/plugin/debug/tokudb_backup.so
[  241s] 
[  241s]     Macro expanded in comment on line 1279: %{_libdir}/mysql/libHotBackup.so
[  241s] 
[  241s]     Macro expanded in comment on line 1280: %{_includedir}/backup.h
[  241s] 
[  241s] 
[  241s] RPM build errors:
[  241s]     Bad exit status from /var/tmp/rpm-tmp.ZzbP1E (%build)
[  241s] 
[  241s] localhost.localdomain failed "build greatsql.spec" at Sat Apr 19 03:45:06 UTC 2025.
[  241s] 

Build failed with exit code 1
```







### 编译报错排查



首先, 搜索clang编译器的 `-fgcc-compatible` 编译选项相关信息.

在 Google 中搜索关键字 `clang flag  fgcc-compatible` , 未搜索到任何相关信息.

>怀疑  `-fgcc-compatible` 这个编译选项是否真的存在?



接着, 排查 `-fgcc-compatible` 这个编译选项是被哪个文件引入的.

使用命令 `fd -tf -x grep -l "fgcc-compatible"` 在相关文件夹中排查, 发现该编译选项来自, 以下文件:

```
// 应该是 osc build 所使用的
// /var/tmp/build-root/standard_riscv64-riscv64/usr/lib/rpm/openEuler/macros

// oe 25.03 系统中的
// /usr/lib/rpm/openEuler/macros
```



文件中相关内容如下:

```
%_general_options         -O2 -g -grecord-gcc-switches -pipe -fstack-protector-strong %[ "%{toolchain}" == "clang" ? "-fgcc-compatible" : "" ]

```



将 `/var/tmp/build-root/standard_riscv64-riscv64/usr/lib/rpm/openEuler/macros` 文件中的 `-fgcc-compatible` 去掉, 重新编译.



### 修改后编译成功











