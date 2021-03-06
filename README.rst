===============
Embeddable luce
===============
-----------------------------------------------------------------------------------------------
Create standalone executables for luce applications for the most common platforms (in one shot)
-----------------------------------------------------------------------------------------------

**KINDLY NOTE: THESE HACKS ARE NOT FOR THE FAINT OF THE HEART**

These scripts can create standalone applications embedding luce and any
dependency, which can be useful for small and embedded devices, for example,
or to distribute close source applications.

It can also create semi-standalone applications.

The difference with a stock lua or luajit interpreter is that it embeds Luce
and looks in specific paths for a ``main.lua`` class to load. 

This is more intended to distribute open source applications to users not
having lua or luce installed. 

The interpreter can then be presented as an optional download sitting next to
the applications itself.

License
=======

Luce Embeddable is licensed under the terms of the `GPLv3 <http://www.gnu.org/licenses/gpl-3.0.html>`_
for all open source applications.

Luce is licensed under the terms of the `GPLv3 <http://www.gnu.org/licenses/gpl-3.0.html>`_,
with some part being `AGPLv3 <http://www.gnu.org/licenses/agpl-3.0.html>`_
for all open source applications.

To release close-source applications with Luce Embedded and/or Luce, please contact us at

``contact``

``_at``

``peersuasive``

``_dot_``

``com``.


Luce Embeddable is (c) 2014, Peersuasive Technologies

Luce is (c) 2014, Peersuasive Technologies

``squish`` is is the work of Metthew Wild (who is also the creator of `Prosody
<https://prosody.im/>`_, among many other things)

sources and legal informations can be found at `squish <http://matthewwild.co.uk/projects/squish/home>`_

Squish is (C) 2009-2010 Matthew Wild.


Support
=======

I'll support any issue on Linux atm, \*nix (including OS X) will come later; I
won't support nor develop anything for Windows, but feel free to fork and
submit pull requests if you feel comfortable with this platform, I'll accept
anything usable and tested.


Cross Compilers
===============

Global
------

* c++11

* gcc 4.8+ / clang 3.4+

Windows
-------

* MingW (from http://mxe.cc/)

* accessible from ``/opt/mingw`` (with a symlink, for example)


OS X
----

* `OS X Cross <https://github.com/tpoechtrager/osxcross>`_
  
  - with libc++
  - accessible from ``/opt/osxcross``


iOS
---

* `iOS toolchain based on clang for linux <https://code.google.com/p/ios-toolchain-based-on-clang-for-linux/>`_

  - accessible from ``/opt/ios-apple-darwin-11``

* `OS X Cross <https://github.com/tpoechtrager/osxcross>`_
  
  - accessible from ``/opt/osxcross``

  - cross-built llvm's libcxx-3.4 for iOS, requires a bit of hacking on the
    original script (see the `hack`_ at the end of this file)

Android
-------

* android NDK >= r9

  - accessible from ``/opt/android-ndk``

* android SDK

  - accessible from ``/opt/android-sdk``


Overall Status
==============


* alpha stage

* as for Linux, OS X and Windows, really stable and reliable, Luce's
  performances are good enough, even though Luce itself is still in alpha stage

  only tested on a couple of Windows machines, however

* for iOS, works fine but a bit slowly (though clearly usable) on my old iPad 1

* for Android, that's slow as hell (but JUCE itself is slow too on this
  platform, as soon as Graphics is involved); a lot of work needs to be done
  here, first of all probably being to use a NativeActivity instead of the Java's
  wrapper provided by JUCE, though this is more to keep code homogeneous than
  expecting a lot of gain from there; next would be fulfilling JUCE's Android
  implementation (kiosk mode, removing title bar,...)


Requirements
============

Besides the compilers for the wanted targets, Luce's static library must also
be available (except for Android, where the shared library is required), as
well as the squished luce module.

Luce static library must exist in ``sources/[os]/libluce.a``, where ``os`` is
one of ``lin``, ``osx``, ``win``, ``ios``.

For Android, same place, with ``android`` as os name and
``libluce_jni_[arch].so`` as lib name, where ``arch`` can be ``x86`` or
``armeabi-v7a``.


Usage
=====


There are two ways to use this facility:

1. ``FULL_STATIC``: to create a standalone executable embedding all modules,
   interpreter, dependencies and the lua classes,

2. ``STATIC`` (default): to create a semi-standalone executable embedding the
   interpreter, all dependencies and a variable number of lua (Lua/C/C++)
   modules, keeping the lua classes out of the enveloppe.
 

For a FULL_STATIC target, create a squishy containing all the required classes
(see the provided example at ``example/squishy``).

For a STATIC target, depending on what you want to embed, create a squishy accordingly
or none at all.


To embed C/C++ modules, see `Embedding C Modules`_.

.. note::

   When updating the squishy file, take care of the resulting class being
   written in a file named ``oResult.lua``.


to build for a specific platform, run

    .. code:: bash

        make [XCROSS=<plat>] [OPTIONS]


to build for all platforms at once, use

   .. code:: bash

        make allplats [OPTIONS]
   


Available options
-----------------

:XCROSS: cross-compile for the specified target

         can be one of:

         - osx
         - win
         - ios
         - android

         without any specified ``XCROSS``, will build for linux.
          
:STATIC: (default) statically compiled binary, with luce fully embedded
                 
:FULL_STATIC:  statically compiled binary, with luce and lua code fully embedded

:LUA52: compile with lua52
                 (not fully tested on all platforms, works fine with win at least)

:NAME: name of the binary (default: ``Luce Embedded Demo``)

       .. note:: 
          
             when ``STATIC`` is set, binary name results in ``$(NAME)_s$(EXT)``

             when ``LUA52`` is set, binary name results in ``$(NAME)52$(EXT)``

             and when both are set, binary name results in ``$(NAME)52_s$(EXT)``

             Resulting bundled applications can be found in ``build/CONFIG/NAME/OS/``

By default, will create a ``STATIC`` Linux x86_64 glibc 2.13 compatible binary with a Debian package.


.. note::

   When you use luce_embeddable as a git submodule, if you don't want to add
   new commits in the submodule itself, you can override or extend the default
   configuration with the helpers ``Makefile.config`` and ``Makefile.extra``
   (see ``Makefile.config.example`` and ``Makefile.extra.example`` for more
   details)


Embedding C Modules
===================

luce_embedded uses luajit when creating a static of semi-static interpreter.

In order to add new modules to embed, inside ``luajit/src``, one must create and fill:

- ``Makefile.extra``,
- ``extra_lualib.h``,
- and ``extra_lib_init.c``
      
(see the ``*.example`` files for example usages) 

+ put your C/C++ module source code in ``luajit/src/modules/[module]``, according to the details
  provided in extra_* and Makefile.*
  
+ extra headers can be placed in ``luajit/src/extra/``

.. note::

    Embedded modules must be pre-registered in order to be available in the
    luce application, that is a call to
    luaL_register(L,"YOUR_MODULE_PUBLIC_NAME", module_funcs) must occur
    somewhere, somehow in your code.

    Usually, you'll put this at the end of your luaopen_YOUR_MODULE_NAME
    method, but there are other ways to achieve this though this is, in my
    opinion, the simplest.
 
    But whatever you opt to, DON'T put it in global!

.. note::

   none of these files are tracked, this is useful to keep the repo clean when
   used as a submodule, so in order to track your changes, better use symlinks
   for your files and folders pointing to the top level repository.
    
......................

.. _hack:

hack for iOS libc++
===================

May work... May not... Unfortunately, I did this in a hurry and didn't take notes during the process

More or less, follow the process of osxcross's ``build_libcxx.sh``

* put iPhoneOS6.1.sdk (or any other version > 6.0) in ``/opt/osxcross/ios/``

* inside the .sdk, remove any of these, if they exist:

- ``usr/include/c++/v1``
- ``usr/lib/libc++.dylib``
- ``usr/lib/libc++.*.dylib``

* uncompress llvm's libcxx-3.4 in ``/opt/osx/ios/``

* go to ``/opt/osx/ios/libcxx-3.4``, 

* create a ``build`` folder, step inside,

* then adapt and execute the following script:

.. code:: bash

    #!/usr/bin/env bash

    export BUILD_LIBCXX=1

    export IOSCROSS_CCTOOLS_PATH=/opt/ios-apple-darwin-11/usr/bin
    export IOSCROSS_CC=$IOSCROSS_CCTOOLS_PATH/ios-clang
    export IOSCROSS_CXX=$IOSCROSS_CCTOOLS_PATH/ios-clang++

    export IOSCROSS_VERSION=0.5
    export IOSCROSS_OSX_VERSION_MIN=10.5
    export IOSCROSS_TARGET=darwin11
    export IOSCROSS_SDK_VERSION=6.1

    export IOSCROSS_SDK=/opt/osxcross/ios/iPhoneOS6.1.sdk

    cmake .. \
        -DCMAKE_CXX_COMPILER=$IOSCROSS_CXX \
        -DCMAKE_C_COMPILER=$IOSCROSS_CC \
        -DCMAKE_SYSTEM_NAME=Darwin \
        -DCMAKE_OSX_SYSROOT=$IOSCROSS_SDK \
        -DLIBCXX_ENABLE_SHARED=No \
        -DCMAKE_BUILD_TYPE=Release \
        -DCMAKE_INSTALL_PREFIX=$IOSCROSS_SDK/../libcxx_$IOSCROSS_SDK_VERSION \
        -DCMAKE_AR=$IOSCROSS_CCTOOLS_PATH/arm-apple-$IOSCROSS_TARGET-ar \
        -DCMAKE_RANLIB=$IOSCROSS_CCTOOLS_PATH/arm-apple-$IOSCROSS_TARGET-ranlib \
        -DCMAKE_CXX_FLAGS="-arch armv7 -arch armv7s" || cmake_error

    export BUILD_LIBCXX=2

    make -j4
    make install

    unset BUILD_LIBCXX

    ## test if it compiles
    /opt/ios-apple-darwin-11/usr/bin/ios-clang++ \
        -I../include test_libcxx.cpp -stdlib=libc++ -std=c++11 -Wall -o testx


test_libcxx.cpp
---------------

.. code:: c++

    #include <list>
    #include <typeindex>
    #include <type_traits>
    #include <chrono>
    #include <tuple>
    #include <scoped_allocator>
    #include <cstdint>
    #include <cinttypes>
    #include <system_error>
    #include <array>
    #include <forward_list>
    #include <unordered_set>
    #include <unordered_map>
    #include <random>
    #include <ratio>
    #include <cfenv>
    #include <codecvt>
    #include <regex>
    #include <thread>
    #include <mutex>
    #include <future>
    #include <condition_variable>
    #include <ctgmath>
    #include <cstdbool>

    #include <iostream>

    int main()
    {
        auto test = []() -> int
        {
            return 0;
        };

        std::mutex m;
        std::thread t(test);
        t.join();

        std::cout << "Hello World!" << std::endl;

        return 0;
    }


.. vim:syntax=rst:filetype=rst:spelllang=en
