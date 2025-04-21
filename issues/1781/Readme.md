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

摘要：按照 GreatSQL 8.0.32-26 测试用例要求，部署 openEuler 25.03 测试镜像环境，对 GreatSQL 的源码编译、RPM安装、主要功能进行测试。





## 测试结论概述



目前，在OBS仓库中的 https://build.tarsier-infra.isrc.ac.cn/package/show/Mega:25.03:Everything/greatsql  构建情况为[failed](https://build.tarsier-infra.isrc.ac.cn/package/live_build_log/Mega:25.03:Everything/greatsql/standard_riscv64/riscv64)。

---



在 Qemu RISCV64 中启动 openEuler 25.03测试镜像，在此基础上进行 GreatSQL 8.0.32-26 测试，GreatSQL 包从 `Mega:25.03:Everything/greatsql` 仓库中拉取.

`osc build standard_riscv64 riscv64`编译, 编译报错， **历经多次编译报错**， 修改源代码， 重新打tar包后， 重新编译， **最终编译成功**。（**单次`osc build` 需要超过10小时**）

先后修改源代码:

- 构建环境中的

  - `/usr/lib/rpm/openEuler/macros`

- greatsql中的

  - `extra/coredumper/src/thread_lister.c`

  - `plugin/innodb_memcached/daemon_memcached/daemon/memcached.c`

- boost中的

  - `boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp`



最终，编译成功，构建了RPM包，可以正常安装，并成功运行起greasql.

测试部分，使用 **mysqlslap** 测试， 指定生成的 SQL 负载类型为混合模式（`mixed`），即测试中将包含 SELECT 查询、INSERT 插入、UPDATE 更新和 DELETE 删除等多种操作。**mysql_client_test** 测试，涵盖基础数据操作（增删改查、建删表/重命名）、数据类型支持（整型、浮点、字符串、日期、DECIMAL 等）、事务控制（InnoDB 事务）、预处理语句（参数绑定、多语句执行、空值处理）、复杂查询逻辑（视图、子查询、联合查询、表连接）。

**测试结果良好 ，显示 GreatSQL 主要功能可以正常使用。**





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



重新编译, 依然报错, 其中主要报错内容如下:



```
[  756s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/greatsql-8.0.32-26/extra/coredumper/src/thread_lister.c:57:15: error: call to undeclared function 'getpid'; ISO C99 and later do not support implicit function declarations [-Wimplicit-function-declaration]
[  756s]    57 |   pid_t pid = getpid();
[  756s]       |               ^
[  756s] 1 warning and 1 error generated.
[  756s] make[2]: *** [extra/coredumper/src/CMakeFiles/coredumper.dir/build.make:90: extra/coredumper/src/CMakeFiles/coredumper.dir/thread_lister.c.o] Error 1
[  756s] make[1]: *** [CMakeFiles/Makefile2:5646: extra/coredumper/src/CMakeFiles/coredumper.dir/all] Error 2
[  756s] make[1]: *** Waiting for unfinished jobs....
```





`extra/coredumper/src/thread_lister.c` 文件内容如下:

```c
/* Copyright (c) 2005-2007, Google Inc.
 * All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without
 * modification, are permitted provided that the following conditions are
 * met:
 *
 *     * Redistributions of source code must retain the above copyright
 * notice, this list of conditions and the following disclaimer.
 *     * Redistributions in binary form must reproduce the above
 * copyright notice, this list of conditions and the following disclaimer
 * in the documentation and/or other materials provided with the
 * distribution.
 *     * Neither the name of Google Inc. nor the names of its
 * contributors may be used to endorse or promote products derived from
 * this software without specific prior written permission.
 *
 * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
 * A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
 * OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
 * SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
 * LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
 * DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
 * THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
 * (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
 * OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 *
 * ---
 * Author: Markus Gutschke
 */

#include "thread_lister.h"

#include <stdio.h> /* needed for NULL on some powerpc platforms (?!) */
#include <sys/prctl.h>

#include "linuxthreads.h"
/* Include other thread listers here that define THREADS macro
 * only when they can provide a good implementation.
 */

#ifndef THREADS

/* Default trivial thread lister for single-threaded applications,
 * or if the multi-threading code has not been ported, yet.
 */

int ListAllProcessThreads(void *parameter, ListAllProcessThreadsCallBack callback, ...) {
  int rc;
  va_list ap;

  int dumpable = prctl(PR_GET_DUMPABLE, 0);
  if (!dumpable) prctl(PR_SET_DUMPABLE, 1);
  va_start(ap, callback);
  pid_t pid = getpid();
  rc = callback(parameter, 1, &pid, ap);
  va_end(ap);
  if (!dumpable) prctl(PR_SET_DUMPABLE, 0);
  return rc;
}

int ResumeAllProcessThreads(int num_threads, pid_t *thread_pids) { return 1; }

#endif

```



在 `extra/coredumper/src/thread_lister.c` 文件开头加上 `#include <unistd.h>`  后, 尝试重新编译.



修改后依然报错， 报错内容主要如下：



```c
[ 5211s] [ 52%] Building C object plugin/innodb_memcached/innodb_memcache/CMakeFiles/innodb_engine.dir/cache-src/assoc.c.o
[ 5212s] clang: warning: argument unused during compilation: '-fstack-clash-protection' [-Wunused-command-line-argument]
[ 5212s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/greatsql-8.0.32-26/plugin/innodb_memcached/daemon_memcached/daemon/memcached.c:4073:18: error: incompatible pointer to integer conversion initializing 'char' with an expression of type 'char *'; dereference with * [-Wint-conversion]
[ 5212s]  4073 |             bool next_get = (key_token + 1)->value;
[ 5212s]       |                  ^          ~~~~~~~~~~~~~~~~~~~~~~
[ 5212s]       |                             *

```





`plugin/innodb_memcached/daemon_memcached/daemon/memcached.c` 文件中，存在类型转化错误。



`static inline char* process_get_command(conn *c, token_t *tokens, size_t ntokens, bool return_cas) ` 函数中的 `bool next_get = (key_token + 1)->value;` , 

```c
bool next_get  // 是 bool 类型 （aka, char类型）
= (key_token + 1)->value;  // 这里的 value 是 char* 类型
```



这里的意图应该是判断 `value` 是否为空。



修改该文件中，该行代码为：

```c
bool next_get = ((key_token + 1)->value != NULL);
```







编译依然报错， 报错如下：



```
:
[10499s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'udt_builtin_mixture_enum' [-Wenum-constexpr-conversion]
[10499s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'int_float_mixture_enum' [-Wenum-constexpr-conversion]
[10511s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'udt_builtin_mixture_enum' [-Wenum-constexpr-conversion]
[10511s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'int_float_mixture_enum' [-Wenum-constexpr-conversion]
[10585s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'udt_builtin_mixture_enum' [-Wenum-constexpr-conversion]
[10586s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'int_float_mixture_enum' [-Wenum-constexpr-conversion]
[10593s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'udt_builtin_mixture_enum' [-Wenum-constexpr-conversion]
[10593s] /home/abuild/rpmbuild/BUILD/greatsql-8.0.32-26/boost_1_77_0/boost/mpl/aux_/integral_wrapper.hpp:73:31: error: integer value -1 is outside the valid range of values [0, 3] for the enumeration type 'int_float_mixture_enum' [-Wenum-constexpr-conversion]
[10594s] 1 warning and 2 errors generated.
[10602s] 1 warning and 2 errors generated.

```



根据相关报错， 搜索到 boost 库的 issue https://github.com/boostorg/mpl/issues/69

其中提到：

> It will be fixed in the next release, 1.86.



为了在当前 boost 版本运行， 仿照 https://github.com/boostorg/mpl/pull/77 修改 文件 `boost/mpl/aux_/integral_wrapper.hpp`

修改为：

```c
#if BOOST_WORKAROUND(__EDG_VERSION__, <= 243) \
    || __cplusplus >= 201103L
 private:
```



重新编译。



### 最终编译成功



最终，终于编译通过了：

```
[37705s] localhost.localdomain finished "build greatsql.spec" at Wed Apr 16 14:51:41 UTC 2025.
[37705s] 

/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/SRPMS/greatsql-8.0.32-26.6.oe2503.src.rpm

/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-test-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-shared-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-client-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-mysql-router-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-icu-data-files-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-devel-8.0.32-26.6.oe2503.riscv64.rpm
/var/tmp/build-root/standard_riscv64-riscv64/home/abuild/rpmbuild/RPMS/riscv64/greatsql-server-8.0.32-26.6.oe2503.riscv64.rpm
```



编译 greatsql ，在 QEMU 16 CPU, 32G RAM 的配置下， **单单OSC BUILD就使用了 10 个多小时**。 

我手头能用的计算资源对这个任务显得捉襟见肘， 对日常学习工作影响较大。



### RPM包安装



根据官方文档 https://greatsql.cn/docs/8.0.32-24/3-quick-start/3-1-quick-start-with-rpm.html ， 安装。



首先安装依赖`yum install -y pkg-config perl libaio-devel numactl-devel numactl-libs net-tools openssl openssl-devel jemalloc jemalloc-devel perl-Data-Dumper perl-Digest-MD5`

然后安装编译后的rpm包 `rpm -ivh --nodeps greatsql*rpm`

安装成功：

```
root@localhost /v/t/b/s/h/a/r/R/riscv64# rpm -ivh --nodeps greatsql*rpm
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:greatsql-shared-8.0.32-26.6.oe250################################# [ 14%]
   2:greatsql-client-8.0.32-26.6.oe250################################# [ 29%]
   3:greatsql-icu-data-files-8.0.32-26################################# [ 43%]
   4:greatsql-server-8.0.32-26.6.oe250################################# [ 57%]
   5:greatsql-test-8.0.32-26.6.oe2503 ################################# [ 71%]
   6:greatsql-mysql-router-8.0.32-26.6################################# [ 86%]
   7:greatsql-devel-8.0.32-26.6.oe2503################################# [100%]

```



### 运行 greatsql



```
root@localhost ~/M/greatsql# vim /lib/systemd/system/mysqld.service
root@localhost ~/M/greatsql# systemctl daemon-reload
root@localhost ~/M/greatsql# systemctl start mysqld
root@localhost ~/M/greatsql# systemctl status mysqld
● mysqld.service - MySQL Server
     Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; preset: disabled)
     Active: active (running) since Thu 2025-04-17 07:33:03 CST; 8s ago
       Docs: man:mysqld(8)
             http://dev.mysql.com/doc/refman/en/using-systemd.html
    Process: 62710 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
   Main PID: 62805 (mysqld)
     Status: "Server is operational"
     Memory: 554.5M ()
     CGroup: /system.slice/mysqld.service
             └─62805 /usr/sbin/mysqld

Apr 17 07:32:39 localhost.localdomain systemd[1]: Starting MySQL Server...
Apr 17 07:32:58 localhost.localdomain (mysqld)[62805]: mysqld.service: Referenced but unset environment variable evaluates to an e>
Apr 17 07:33:03 localhost.localdomain systemd[1]: Started MySQL Server.


root@localhost ~/M/greatsql# grep -i root /var/log/mysqld.log
2025-04-16T23:32:48.390512Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: bs/u<7I&kxt.


root@localhost ~/M/greatsql# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.32-26

Copyright (c) 2021-2024 GreatDB Software Co., Ltd
Copyright (c) 2009-2024 Percona LLC and/or its affiliates
Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> \s
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> alter user user() identified by 'GreatSQL@202X';
Query OK, 0 rows affected (0.08 sec)

```



成功登陆并执行命令

```
mysql> \s
--------------
mysql  Ver 8.0.32-26 for Linux on riscv64 (GreatSQL (GPL), Release 26, Revision a68b3034c3d)

Connection id:          8
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.32-26
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    utf8mb4
Conn.  characterset:    utf8mb4
UNIX socket:            /var/lib/mysql/mysql.sock
Binary data as:         Hexadecimal
Uptime:                 1 min 37 sec

Threads: 2  Questions: 7  Slow queries: 1  Opens: 133  Flush tables: 3  Open tables: 49  Queries per second avg: 0.072
--------------

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.06 sec)

```





### mysqlslap 测试

mysqlslap是mysql自带的基准测试工具. 

以下测试指定生成的 SQL 负载类型为混合模式（`mixed`），即测试中将包含 SELECT 查询、INSERT 插入、UPDATE 更新和 DELETE 删除等多种操作。



```
root@localhost /v/t/b/s/h/a/r/R/riscv64# mysqlslap -uroot -pGreatSQL@202X --concurrency=50 --iterations=10 --auto-generate-sql --auto-generate-sql-load-type=mixed --auto-generate-sql-add-autoincrement
mysqlslap: [Warning] Using a password on the command line interface can be insecure.
Benchmark
        Average number of seconds to run all queries: 0.260 seconds
        Minimum number of seconds to run all queries: 0.246 seconds
        Maximum number of seconds to run all queries: 0.294 seconds
        Number of clients running queries: 50
        Average number of queries per client: 0


```





### mysql_client_test 测试

mysql_client_test 是 MySQL 提供的一个测试工具，主要是一个专注于 MySQL 客户端库测试的工具，适用于开发者和数据库管理员验证客户端库的功能、性能和兼容性。

测试用例涵盖基础数据操作（增删改查、建删表/重命名）、数据类型支持（整型、浮点、字符串、日期、DECIMAL 等）、事务控制（InnoDB 事务）、预处理语句（参数绑定、多语句执行、空值处理）、复杂查询逻辑（视图、子查询、联合查询、表连接）。



运行以下测试：

```
#!/bin/bash

# MySQL 连接参数
HOST="127.0.0.1"
USER="root"
PASSWORD="GreatSQL@202X"

# 测试用例列表
TEST_CASES=(
    "client_query"
    "test_insert"
    "test_update"
    "test_simple_delete"
    "test_select"
    "test_create_drop"
    "test_rename"
    "test_fetch_long"
    "test_fetch_short"
    "test_fetch_tiny"
    "test_fetch_bigint"
    "test_fetch_float"
    "test_fetch_double"
    "test_fetch_str"
    "test_fetch_date"
    "test_temporal_param"
    "test_decimal_bug"
    "test_tran_innodb"
    "test_simple_update"
    "test_simple_delete"
    "test_prepare"
    "test_prepare_simple"
    "test_prepare_noparam"
    "test_ps_null_param"
    "test_bind_result"
    "test_bind_result_ext"
    "test_prepare_multi_statements"
    "test_view"
    "test_view_where"
    "test_view_star"
    "test_subselect"
    "test_union"
    "test_join"
    "test_left_join_view"
    "test_warnings"
    "test_parse_error_and_bad_length"
    "test_time_zone"
)

# 初始化失败用例数组
FAILED_CASES=()

# 逐一运行测试用例
for TEST_CASE in "${TEST_CASES[@]}"; do
    echo "----------------------------------------"
    echo "Running test case: $TEST_CASE"
    
    # 执行测试并直接输出到控制台
    mysql_client_test \
        --host="$HOST" \
        --user="$USER" \
        --password="$PASSWORD" \
        "$TEST_CASE"
    
    # 检查测试是否成功
    if [ $? -eq 0 ]; then
        echo "Test case '$TEST_CASE' completed successfully."
    else
        echo "Test case '$TEST_CASE' failed. Check the output above for details."
        FAILED_CASES+=("$TEST_CASE")
    fi
    
    echo "----------------------------------------"
done

# 输出所有测试结果
echo "All test cases have been executed."
echo ""

# 如果有失败的用例，输出它们
if [ ${#FAILED_CASES[@]} -gt 0 ]; then
    echo "Failed test cases (${#FAILED_CASES[@]}):"
    for FAILED_CASE in "${FAILED_CASES[@]}"; do
        echo "  - $FAILED_CASE"
    done
else
    echo "All test cases passed successfully!"
fi

```



mysql_client_test 以上的testcase 全部通过。







