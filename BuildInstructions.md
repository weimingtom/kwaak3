This page describes how to compile Kwaak3 for Android.

# Introduction #

Prerequisites:
  * Android SDK >= 1.6
  * Android NDK 1.6
  * (optionally) gcc 4.4.0 from android git to create a Neon optimized build

# Building #
Kwaak3 consists of three components:
  * Quake3 engine (libquake3.so or libquake3\_neon.so) + game libraries (libcgamearm.so, libuiarm.so, libqagamearm.so)
  * JNI library for loading the right libquake3.so library: libkwaakjni.so
  * Kwaak3 Java side

This section describes how to build each part.

## Building Quake3 ##
  * Open Makefile.local which can be found in the ioquake3 directory.
  * Set ANDROID\_NDK\_DIR to the correct directory.
  * Set CC to the C compiler you want to use. If unsure set it to the gcc version bundled with the NDK. If you want to build with Neon support, you need GCC 4.4.0 which can be found in the Android git repository (there is a precompiled Linux version in there). Other compilers like the ones from Codesourcery won't work. You will end up with issues at the linking stage.
  * Uncomment the ANDROID\_CFLAGS variable depending on which quake3 version you want to compile. If not compiling for Neon, select the one containing the ARMv5 target.
  * Save Makefile.local
  * Type 'make' to compile it all
  * You will find libquake3.so in build/release-linux-arm and the libcgamearm/libuiarm/libqagamearm are in the baseq3 subdirectory.

## Building KwaakJNI ##
  * Copy the kwaak directory to the 'apps' directory of the NDK. Then execute 'make APP=kwaak' from the root of the NDK directory. (There might be a better way for this but I'm not aware of it).
  * The JNI library should appear in apps/kwaak/libs/armeabi

## Building Java side ##
  * Copy libquake3.so and the Quake3 game libraries to the apps/kwaak/libs/armeabi directory. (In case you are building for neon, rename libquake3.so to libquake3\_neon.so)
  * Load the Kwaak3 project in Eclipse and it should compile.
  * Run the game :) If you encounter issues check 'logcat', the game might be showing an error. Likely it can't load a library.