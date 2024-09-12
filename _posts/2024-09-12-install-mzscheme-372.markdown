---
layout: post
title: "Failed to Compile MzScheme from Source"
categories: Linux compilation  
---

Download MzScheme source code for Unix from: http://download.plt-scheme.org/mzscheme/mz-372-src-unix-tgz.html

I first untar the package and try compile:

````bash
wget http://download.plt-scheme.org/bundles/372/mz/mz-372-src-unix.tgz
tar xf mz-372-src-unix.tgz
# read manual before compiling it
vim src/README
cd src
./configure
````

I got first compilation error:
````
checking how to run the C++ preprocessor... /lib/cpp
configure: error: C++ preprocessor "/lib/cpp" fails sanity check
See `config.log' for more details.
````

Let's check what `/lib/cpp` is:

````bash
myself@VM-0-4-ubuntu:/script/mz-372/src$ ll /lib/cpp
lrwxrwxrwx 1 root root 21 Jan 31  2024 /lib/cpp -> /etc/alternatives/cpp*
myself@VM-0-4-ubuntu:/script/mz-372/src$ update-alternatives --list cpp
/usr/bin/cpp
myself@VM-0-4-ubuntu:/script/mz-372/src$ /usr/bin/cpp -v
...
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 13.2.0 (Ubuntu 13.2.0-23ubuntu4)
...
````

I suppose this version is too new for compiling our binary or it is missing a c++ installation.

````bash
myself@VM-0-4-ubuntu:/script/mz-372/src$ sudo apt install g++ -y
myself@VM-0-4-ubuntu:/script/mz-372/src$ CC=/usr/bin/gcc CXX=/usr/bin/g++ ./configure --prefix=/data/mzscheme/ 
...
>>> Installation targets:
 executables  : ${exec_prefix}/bin/...
 Scheme code  : ${exec_prefix}/lib/plt/collects/...
 core docs    : ${prefix}/share/plt/doc/...
 C libraries  : ${exec_prefix}/lib/...
 C headers    : ${prefix}/include/plt/...
 extra C objs : ${exec_prefix}/lib/plt/...
 man pages    : ${prefix}/man/...
  where prefix = /data/mzscheme
    and exec_prefix = ${prefix}
....
myself@VM-0-4-ubuntu:/script/mz-372/src$ make
...
/usr/bin/gcc -o mzschemecgc main.o  libmzscheme.a libmzgc.a  -ldl -lm  -rdynamic 
/usr/bin/ld: libmzscheme.a(gmp.o): in function `scheme_gmpn_add_1':
/script/mz-372/src/mzscheme/src/./gmp/gmp.h:396: multiple definition of `scheme_gmpn_add_1'; libmzscheme.a(bignum.o):/script/mz-372/src/mzscheme/src/./gmp/gmp.h:396: first defined here
/usr/bin/ld: libmzscheme.a(gmp.o): in function `scheme_gmpn_add':
/script/mz-372/src/mzscheme/src/./gmp/gmp.h:441: multiple definition of `scheme_gmpn_add'; libmzscheme.a(bignum.o):/script/mz-372/src/mzscheme/src/./gmp/gmp.h:441: first defined here
/usr/bin/ld: libmzscheme.a(gmp.o): in function `scheme_gmpn_sub_1':
/script/mz-372/src/mzscheme/src/./gmp/gmp.h:468: multiple definition of `scheme_gmpn_sub_1'; libmzscheme.a(bignum.o):/script/mz-372/src/mzscheme/src/./gmp/gmp.h:468: first defined here
/usr/bin/ld: libmzscheme.a(gmp.o): in function `scheme_gmpn_sub':
/script/mz-372/src/mzscheme/src/./gmp/gmp.h:513: multiple definition of `scheme_gmpn_sub'; libmzscheme.a(bignum.o):/script/mz-372/src/mzscheme/src/./gmp/gmp.h:513: first defined here
/usr/bin/ld: warning: unix64.o: missing .note.GNU-stack section implies executable stack
/usr/bin/ld: NOTE: This behaviour is deprecated and will be removed in a future version of the linker
collect2: error: ld returned 1 exit status
make[4]: *** [Makefile:139: mzschemecgc] Error 1
make[4]: Leaving directory '/script/mz-372/src/mzscheme'
make[3]: *** [Makefile:66: cgc] Error 2
make[3]: Leaving directory '/script/mz-372/src/mzscheme'
make[2]: *** [Makefile:69: 3m] Error 2
make[2]: Leaving directory '/script/mz-372/src/mzscheme'
make[1]: *** [Makefile:29: 3m] Error 2
make[1]: Leaving directory '/script/mz-372/src'
make: *** [Makefile:26: all] Error 2
...
````

I tried several ways to fix this duplicate definition error:
1. read source code and remove `define` for a related part
2. use CC and CXX to specify compilers
3. clean directory and build from start
   
but they all didn't work in my case. I think the final way to fix is to install a lower version of gcc and retry. I will write a new post if I can fix this problem. If you find another way to do it, please feel free to email me.
