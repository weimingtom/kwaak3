This is a port of Quake3 to Android.

# Project #
When I received a Motorola Milestone phone, I wanted to get into Android development. I had seen ports of Quake3 to the iphone and the N900 which have similar specifications (all use a similar CPU and the PowerVR GPU), so I thought why not bring Quake3 to Android. As a start point I used the N900 version of ioquake3. It only took me a day to get the game to compile and to load the Quake3 main-function. After that it took me a few more days to get OpenGL and some input working. In the weeks following the initial port I added touchscreen support, networking and sound.

# Features #
  * Audio
  * Hardware floating-point on Neon-capable devices
  * Input: keyboard, touchscreen and trackball
  * OpenGL 3D rendering
  * Game uses ARM optimized libcgamearm.so / libqaarm.so / libuiarm.so
  * Networking

# Performance #
Normally when you compile C/C++ code using the Android NDK, the compiler targets a generic ARMv5 CPU which uses software floating-point. Without any optimizations and audio Quake3 runs at 22fps. Since Quake3 uses a lot of floating-point calculations. I tried a better C-compiler (GCC 4.4.0 from Android GIT) which supports modern CPUs and Neon SIMD instructions. Quake3 optimized for Cortex-A8 with Neon is about 15% faster without audio and 35% with audio compared to the generic ARMv5 build. Most likely the performance improvement compared to the ARMv5 build is not that big because the system libraries of the Milestone have been compiled with FPU support, so sin/cos/log/.. take advantage of the FPU.

One of my users also tested the game on his G1 but the device doesn't have much RAM and the CPU runs is downclocked to a little more than 300MHz. In the timedemo it scores about 10fps without audio. In game (after some tweaking) it can reach upto 20fps in not so busy areas.

Another user managed to run Quake3 on his HTC Hero (see http://code.google.com/p/kwaak3/issues/detail?id=6).

Below are benchmark results of Quake3 with and without audio. All benchmarks are run using demo four at the default Quake3 settings: In case of the Milestone the resolution is 854x480 and the hero uses 480x320.

|          | **Milestone (ARMv5)** | **Milestone (Neon)** | **Nexus One (Neon + Froyo)** | **HTC Hero** |
|:---------|:----------------------|:---------------------|:-----------------------------|:-------------|
| No audio | 22.0 fps              | 25.4 fps             | 19.5 fps                     | 14.5 fps     |
| Audio    | 14.7 fps              | 20.0 fps             | 21.3 fps                     | 11.5 fps     |

Performance can be improved quite a bit by tweaking Quake3 settings. Setting lighting to Vertex instead of Lightmap improves performs by 20% to more than 30fps in Neon mode without audio.

# Design #
Android programs are written in Java and performance critical parts can be implemented in a C library and can then be called using JNI. 99.9% of the code in this project is C and the other part is Java. The small Java part initializes OpenGL, handles input and manages audio. The Kwaak3 Java code communicates with a 'libquake3.so' using a libkwaakjni wrapper. This wrapper loads either a generic ARMv5 'libquake3.so' or a Neon optimized 'libquake3\_neon.so' depending on the CPU.

  * Ioquake3 contains an 'infinite' loop which renders a single frame each iteration. A single iteration of this loop has been turned into a function which is called from onDrawFrame in Java.
  * Input is processed by Android and is sent to the game when it is received.
  * Audio is implemented on the Java side and using JNI, kwaak3 requests a number of audio samples when it needs some.

# Supported devices #
During development I have only had access to a Motorola Milestone. This phone has a keyboard and for this reason the game has been optimized for devices with a keyboard, so it won't work on your Nexus One or other phone at this point. One of my testers used a G1 and thanks to his testing there is also some trackball support. If I had my hands on more devices I would add support for those devices. The minimum required Android version at this point is 1.6.

Note the G1 doesn't have much RAM and due to this is very sensitive to crashing. The game might be more stable if you disable audio.

# Future work #
  * Audio: use direct buffers to make data copying to Java more efficient and optimize the mixing code using Neon instructions
  * 3D rendering: use VBOs for uploading geometry and prevent unneeded re-uploads
  * Input: improve controls on phones without keyboard like the Nexus One
  * Profile: profile the Quake3 code and optimize slow parts

# Screenshots #
![http://kwaak3.googlecode.com/files/kwaak3.jpg](http://kwaak3.googlecode.com/files/kwaak3.jpg)

# Installation #
  * Download the [kwaak3.apk](http://kwaak3.googlecode.com/files/kwaak3.apk) from the download section and install it on your phone. (Note you need at least Android 1.6)
  * Create a directory quake3 in the root of your sdcard and also create a baseq3 directory inside it.
  * Copy pak0.pk3 of your Quake3 CD (or from the demo) to the quake3/baseq3 directory.
  * Download the Quake3 1.32 point release and copy pak1-pak8.pk3 to quake3/baseq3
  * Start the game :)

![http://kwaak3.googlecode.com/files/kwaak3_qr.png](http://kwaak3.googlecode.com/files/kwaak3_qr.png)

# Controls #
The game is controlled using the keyboard, touch screen and trackball (if available). The most important controls are:
  * Movement: a/s/w/d, up/down arrows
  * Look: touch screen, trackball and left/right arrows
  * Shooting: Alt / D-pad center / tap on screen / trackball
  * Tilde (quake3 console): search button
  * Escape: back button

# FAQ #
  * Why does the application require reading the phone state and identity?
> Kwaak3 only requires access to the sdcard (external storage permission) and internet support for networking. On some Android devices (at least on the HTC Tattoo and HTC Magic which run Android 1.6) also phone state reading is required for some reason. This is not the case on the Droid/Milestone or on the Nexus One.

  * Why does Kwaak3 require Android 1.6?
> Version 1.6 of the Android Native Development Kit (NDK) added support for OpenGL ES which is required for 3D rendering. Further I don't think it would make sense to run the game on older Android versions since you need a good CPU (with a FPU) and GPU for running the game. Most Android 1.x devices aren't powerful enough. The game is best served by a Cortex A8 like used in the Droid/Milestone or a Snapdragon as used in the Acer Liquid or Nexus One. Note I don't know how well the game performs on other devices except for the G1, it might be that lets say a HTC Hero runs it a lot better because the CPU isn't downclocked that much and because it has more RAM.

  * Why is the cd-key menu shown?
> The cd-key menu is part of the original Quake3 game. A valid key is needed for online play but as you notice you can skip it (I think it is because ioquake3 relaxed the check a bit).

  * Why can't I run demos from the Quake3 menu?
> For some reason this doesn't work and I'm not sure why. I remember seeing it in normal Quake3 / ioquake3 before as well. You can always run demos from the quake3 console e.g. 'demo four'. If you want to benchmark the game make sure to type 'timedemo 1' before starting the demo. The framerate will appear in the quake3 console once the demo finishes.

  * Can I play Kwaak3 online?
> There is network support in Quake3 which works properly. I played against my laptop using wifi and it works fine. Online gaming should also work but by default you can only connect to pure servers since I'm using precompiled quake3 libraries instead of the qvm bytecode (you could use the interpreter by playing with q3 console variables if you want but the performance will decrease a lot). Since the quake3 libraries can't be verified (and it can contain cheats) not all servers accept this. There is also no punkbuster support but none of the Quake3 ports have it because the code for that wasn't released.
  * Haven't there been other Android ports of Quake3?
> If you search on Google, you will find 'Auriga3D'. It is a portable game engine written in Java which can load Quake3 maps but it is not a port of Quake3.
  * Does Kwaak3 support OpenArena and other mods?
> The ioquake3 engine supports mods like OpenArena properly. It should be easy to support them in Kwaak3 as well, so that you don't need any original Quake3 data. At this point mods aren't supported because I have added a check to see if pak0-pak8.pk3 are installed. I will add this in a future version.
  * Why is Kwaak3 not on the Android market?
> At some point I might upload it to the Market. I would do it at a point where OpenArena and friends work fine. Further on the market it would just be called Kwaak3 and I would show screenshots of OpenArena and other mods and not of the original game and I also wouldn't use the original name anywhere, since I don't want to get into troubles with ID Software.
