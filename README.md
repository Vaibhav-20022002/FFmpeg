FFmpeg README
=============

FFmpeg is a collection of libraries and tools to process multimedia content
such as audio, video, subtitles and related metadata.

## Libraries

* `libavcodec` provides implementation of a wider range of codecs.
* `libavformat` implements streaming protocols, container formats and basic I/O access.
* `libavutil` includes hashers, decompressors and miscellaneous utility functions.
* `libavfilter` provides means to alter decoded audio and video through a directed graph of connected filters.
* `libavdevice` provides an abstraction to access capture and playback devices.
* `libswresample` implements audio mixing and resampling routines.
* `libswscale` implements color conversion and scaling routines.

## Tools

* [ffmpeg](https://ffmpeg.org/ffmpeg.html) is a command line toolbox to
  manipulate, convert and stream multimedia content.
* [ffplay](https://ffmpeg.org/ffplay.html) is a minimalistic multimedia player.
* [ffprobe](https://ffmpeg.org/ffprobe.html) is a simple analysis tool to inspect
  multimedia content.
* Additional small tools such as `aviocat`, `ismindex` and `qt-faststart`.

## Documentation

The offline documentation is available in the **doc/** directory.

The online documentation is available in the main [website](https://ffmpeg.org)
and in the [wiki](https://trac.ffmpeg.org).

### Examples

Coding examples are available in the **doc/examples** directory.

## License

FFmpeg codebase is mainly LGPL-licensed with optional components licensed under
GPL. Please refer to the LICENSE file for detailed information.

## Contributing

Patches should be submitted to the ffmpeg-devel mailing list using
`git format-patch` or `git send-email`. Github pull requests should be
avoided because they are not part of our review process and will be ignored.


Here’s your polished build-and-deploy guide, no fluff—just the vital steps and commands.

---

## Full Process: Build FFmpeg on Ubuntu

### 1. **Remove Existing FFmpeg (Snap/Apt)**

```bash
sudo apt-get remove --purge -y ffmpeg
sudo apt-get autoremove -y
sudo snap remove ffmpeg || true   # in case a snap version exists
hash -r  # clear bash cache
```

Ensures no stale binaries obstruct your new build.

---

### 2. **Install Build Dependencies**

```bash
sudo apt-get update
sudo apt-get install -y \
  autoconf automake build-essential cmake git-core \
  libass-dev libfreetype6-dev libsdl2-dev libtool \
  libva-dev libvdpau-dev libvorbis-dev libxcb1-dev \
  libxcb-shm0-dev libxcb-xfixes0-dev pkg-config \
  texinfo wget zlib1g-dev nasm yasm
```

Covers tools to build both SVT‑AV1 and FFmpeg.

> You can add other codecs/libraries too. For example, nvidia ones.

---

### 3. **Clone and Install SVT‑AV1 (v2.3.0)** (Optional)

```bash
git clone https://gitlab.com/AOMediaCodec/SVT-AV1.git
cd SVT-AV1
git fetch --tags
git checkout v2.3.0
cd Build
cmake .. -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)
sudo make install
```

This installs the old API-compatible version. Verify:

```bash
pkg-config --modversion SvtAv1Enc
# Expected output: 2.3.0
```

---

### 4. **Clone and Configure FFmpeg**

```bash
cd ~
mkdir -p ffmpeg_sources && cd ffmpeg_sources
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
cd ffmpeg
make distclean
```

Prepare FFmpeg with your full codec support:

```bash
./configure \
  --prefix=/usr/local \
  --enable-shared \
  --enable-pthreads \
  --enable-version3 \
  --enable-ffplay \
  --enable-gnutls \
  --enable-gpl \
  --enable-nonfree \
  \
  # Audio codecs
  --enable-libfdk-aac \
  --enable-libmp3lame \
  --enable-libopus \
  --enable-libspeex \
  --enable-libvorbis \
  --enable-libsoxr \
  \
  # Video codecs
  --enable-libx264 \
  --enable-libx265 \
  --enable-libvpx \
  --enable-libtheora \
  --enable-librav1e \
  --enable-libsvtav1 \
  --enable-libopenjpeg \
  --enable-libwebp \
  --enable-libxvid \
  --enable-libdav1d \
  --enable-libaom \
  \
  # Subtitles and fonts
  --enable-libass \
  --enable-libfreetype \
  --enable-libfontconfig \
  --enable-libharfbuzz \
  \
  # Filters and enhancements
  --enable-libvidstab \
  --enable-librubberband \
  --enable-libzimg \
  \
  # Other libraries
  --enable-libbluray \
  --enable-libtesseract \
  --enable-libxml2 \
  --enable-libsnappy \
  --enable-libsrt \
  --enable-libssh \
  --enable-libzmq \
  --enable-libopencore-amrnb \
  --enable-libopencore-amrwb \
  --enable-lzma

  [other --enable-lib* flags as needed]
```

---

### 5. **Compile and Install FFmpeg**

```bash
make -j$(nproc)
sudo make install
sudo ldconfig
hash -r
```

---

### 6. **Verify Installation**

```bash
which ffmpeg  # should show /usr/local/bin/ffmpeg
ffmpeg -version  # confirm it's your built version
```
