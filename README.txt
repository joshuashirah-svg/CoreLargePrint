CoreLargePrint — CTP100LG CorePrint raster integration
======================================================

This revision replaces the previous generic ESC/POS text/image path with a
CorePrint-style 51 78 print stream reconstructed from the CTP100LG smali data
provided during the project work.

Implemented:
- Paired Bluetooth device picker and Bluetooth Classic SPP connection.
- CorePrint image header: 51 78 BE 00 01 00 00 00 FF.
- CorePrint text header:  51 78 BE 00 01 00 01 07 FF.
- Feed command with CRC-8; feed(20) = 51 78 D3 00 01 00 14 6C FF.
- 200 dpi raster path split into 18-row blocks.
- Java zlib/Deflater compression and 51 78 CE frame lengths / CRC / FF trailer.
- Receipt text rendered to a 576-dot bitmap; image picker, preview, remove, print.
- Bounded 512-byte Bluetooth stream writes.

Important accuracy note:
The recovered frame structure, command headers, zlib method, 18-row block size,
feed packet, and CRC-8 polynomial are grounded in the supplied CorePrint smali
and driver sources. The original app's complete image lookup-table conversion,
full command sequencing and physical printer behavior were not all present in
the four-file driver source archive. This implementation uses a conventional
MSB-first monochrome packing pass (Floyd-Steinberg for images; threshold for
text) to populate those recovered frames. Treat it as a protocol-integrated
build candidate, not a physically verified driver, until the CTP100LG prints
both a text receipt and a selected image.

Build on the Android phone in Termux:
1. Extract this project folder to shared storage or Termux home.
2. Ensure Termux has Gradle, Java 17/21, Android SDK platform 35, build-tools 35,
   and the configured aapt2 binary.
3. Run: bash build-termux.sh
4. The script copies the debug APK to:
   ~/storage/downloads/CoreLargePrint-CTP100LG-CorePrint.apk

If the SDK is not detected automatically, create local.properties containing:
   sdk.dir=/data/data/com.termux/files/home/Android/Sdk
Use the actual SDK path on the phone if different.

The build container used to prepare this source archive does not have an Android
SDK or Gradle installation, so it could not produce/sign an installable APK.
No claim of successful physical printing is made.
