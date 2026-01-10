## What is it

In order to access a serial port in old Java programs, you need the RXTX libraries compiled for your specific hardware. Java uses the [Java Native Interface](http://java.sun.com/j2se/1.5.0/docs/guide/jni/spec/jniTOC.html "Java 1.5 JNI Guide") (JNI) to bridge between your platform-independent application code and the hardware-specific serial port drivers. This repo provides a native platform-dependent binary `librxtxSerial.jnilib` and `RXTXcomm.jar` adapter to talk to serial from Java app.
## CompiIation and installation steps

To make this code buildable and work fine on modern OS X I had to patch `SerialImpl.c` and `SerialImpl.h` manually based on the [patch instructions here](https://web.archive.org/web/20121227100340/https://mailman.qbang.org/pipermail/rxtx/2007-June/3368151.html) and [blog post by Robert Harder here](https://robertharder.wordpress.com/2009/08/18/rxtx-java-6-and-librxtxserial-jnilib-on-intel-mac-os-x/). I also had to introduce some build system tunings for modern autotools
#### Prerequisites
1. [Homebrew](https://brew.sh)  installed
2. Install libtool
   ```bash
   brew install libtool automake
   ```
#### Installing from source
1. Checkout
   ```bash
   git clone git@github.com:romansavrulin/java-rxtx-apple-silicon.git
   cd java-rxtx-apple-silicon
   ```
2. Run configuration and resolve possible issues with dependency (i had none on Sequoia 15.7.3 (24G419))
   ```bash
   ./autogen.sh
   ./configure
   ```
3. Build
   ```bash
   make
   ```
4. Install
   ```bash
   sudo make install
   ```

#### Uninstalling from sources
1. Ensure your project is configured
   ```bash
   ./autogen.sh
   ./configure
   ```
2. Uninstall
   ```bash
   sudo make uninstall
   ```
#### Installing prebuilt binaries

Pre-compiled RXTX native library binaries are located on releases page (Native rxtx-2.1-7 library + patch)

`aarch64-apple-darwin24.6.0.zip` is fresly build by me on Apple M3 machine and tested to work fine with java version `8.0.472.fx-librca`

`i386-intel+ppc.zip` is a 64-bit Intel verion for Java 6 on Mac OS X (also i386, ppc, and ppc64). It was pre-compiled by Robert Harder and provided as is.

- **To install:** copy `librxtxSerial.jnilib` and `RXTXcomm.jar` files from archive with your architecture to `/Library/Java/Extensions` directory.

- **To uninstall:** remove `librxtxSerial.jnilib` and `RXTXcomm.jar` files from `/Library/Java/Extensions` directory

You're welcome to follow build instructions for compiling this library yourself.

To check the available architectures in native binaries run
```bash
$ file aarch64-apple-darwin24.6.0/librxtxSerial.jnilib
aarch64-apple-darwin24.6.0/librxtxSerial.jnilib: Mach-O 64-bit bundle arm64

$ file librxtxSerial.jnilib
librxtxSerial.jnilib: Mach-O universal binary with 2 architectures
librxtxSerial.jnilib (for architecture i386):	Mach-O bundle i386
librxtxSerial.jnilib (for architecture ppc7400): Mach-O bundle ppc
```

### Where this code comes from

- This code comes from [RxTx pub server](http://rxtx.qbang.org/pub/rxtx/) ([2.1-7.zip](http://rxtx.qbang.org/pub/rxtx/rxtx-2.1-7.zip))
- Some description is present on http://rxtx.qbang.org/wiki/index.php/Download

### Known issues
Some people [mention](https://jgrasstechtips.blogspot.com/2008/04/rxtx-and-funky-portinuseexception-on.html) `gnu.io.PortInUseException: Unknown Application` error when using this lib

The PortInUseException rarely has anything to do the port being in use. For reasons I don't understand RXTX is not using `/var/spool/uucp` for lock files. It's using `/var/lock`. A directory that didn't exist on most systems. Once I created this directory I started getting different errors for the RXTX code. This directory requires read/write permissions for the user. I just used the blanket `chmod 777`.

1. In terminal enter the commands
```bash
sudo mkdir /var/lock   (hit enter end type your password)  
sudo chmod 777 /var/lock  (hit enter again)
```

### Other resources

- Prebuilt binaries for all platforms are [here](https://github.com/CMU-CREATE-Lab/commons-java/tree/master/java/lib/rxtx)