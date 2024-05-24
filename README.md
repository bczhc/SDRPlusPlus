sdrpp-server
===

# Downstream changes

1. Allow run in [Termux](https://termux.dev)
2. Close the already-connected clients when new connection establishes
---

# Raspberry Pi

## Build

```bash
./rpi-build
```

# Termux

Start Airspy(HF) SDR++ Server in Termux

<img src="https://github.com/bczhc/sdrpp-termux/assets/49330580/1b49eae2-2d23-4665-8a24-2d558df6ac0d" style="width: 50%">

## Build

1. Clone

   ```shell
   git clone https://github.com/bczhc/SDRPlusPlus
   ```
2. Build sdr-kit from https://github.com/AlexandreRouma/android-sdr-kit

   (I just downloaded its GitHub Action artifacts)

   You will have:
   ```console
   sdr-kit
   ├── arm64-v8a
   ├── armeabi-v7a
   ├── x86
   └── x86_64
   ```

   Then copy `sdr-kit` to the repository root.
3. Build

   ```shell
   cd SDRPlusPlus
   mkdir build
   cd build

   NDK_DIR=/home/bczhc/bin/AndroidSdk/ndk/25.1.8937393

   cmake .. -G Ninja \
   -DANDROID_NDK="$NDK_DIR" \
   -DCMAKE_TOOLCHAIN_FILE="$NDK_DIR"/build/cmake/android.toolchain.cmake \
   -DANDROID_ABI=arm64-v8a \
   -DANDROID_PLATFORM=android-29 \
   -DOPT_BUILD_DISCORD_PRESENCE=OFF \
   -DOPT_BACKEND_GLFW=OFF \
   -DOPT_BACKEND_ANDROID=ON \
   -DOPT_BUILD_AUDIO_SINK=OFF \
   -DOPT_BUILD_AUDIO_SOURCE=OFF \
   -DOPT_BUILD_AIRSPY_SOURCE=ON \
   -DOPT_BUILD_AIRSPYHF_SOURCE=ON \
   -DOPT_BUILD_BLADERF_SOURCE=OFF \
   -DOPT_BUILD_FILE_SOURCE=OFF \
   -DOPT_BUILD_HACKRF_SOURCE=OFF \
   -DOPT_BUILD_HERMES_SOURCE=OFF \
   -DOPT_BUILD_LIMESDR_SOURCE=OFF \
   -DOPT_BUILD_NETWORK_SOURCE=OFF \
   -DOPT_BUILD_PERSEUS_SOURCE=OFF \
   -DOPT_BUILD_PLUTOSDR_SOURCE=OFF \
   -DOPT_BUILD_RFSPACE_SOURCE=OFF \
   -DOPT_BUILD_RTL_SDR_SOURCE=OFF \
   -DOPT_BUILD_RTL_TCP_SOURCE=OFF \
   -DOPT_BUILD_SDRPLAY_SOURCE=OFF \
   -DOPT_BUILD_SDRPP_SERVER_SOURCE=OFF \
   -DOPT_BUILD_SOAPY_SOURCE=OFF \
   -DOPT_BUILD_SPECTRAN_SOURCE=OFF \
   -DOPT_BUILD_SPECTRAN_HTTP_SOURCE=OFF \
   -DOPT_BUILD_SPYSERVER_SOURCE=OFF \
   -DOPT_BUILD_USRP_SOURCE=OFF

   ninja
   ```
4. Assemble

   In the build directory, do:
   ```shell
   mkdir libs
   cp -v ../sdr-kit/arm64-v8a/lib/* libs
   fd --extension=so -HI -x cp -v {} libs
   ```
   This copies all libraries together

5. Copy to Termux

   Connect Android with ADB, then do:
   ```shell
   tar -c sdrpp libs | adb shell run-as "com.termux files/usr/bin/bash -c 'cd files/home; tar -xv'"
   ```

## Run

1. Open Termux App
2. Connect your Airspy(HF) via USB, and grand the permission
   with [Termux-USB](https://wiki.termux.com/wiki/Termux-usb).
3. Create a root folder for SDR++ and link its modules

   ```shell
   mkdir sdrpp-root
   cd sdrpp-root
   ln -s ../libs modules
   cd
   ```

   Then you can run the SDR++ server like this:
   ```shell
   export LD_LIBRARY_PATH=libs
   
   termux-usb -e './sdrpp -s -r sdrpp-root -p <port> -t <device-type> -d' <usb-path>
   # Example below
   # termux-usb -e './sdrpp -s -r sdrpp-root -p 4000 -t 1 -d' "/dev/bus/usb/002/003"

   # or you can also see the 'usb-run' script if you'd like: https://gist.github.com/bczhc/01e1991db29af67c245ba99f5f888ca2
   ```
