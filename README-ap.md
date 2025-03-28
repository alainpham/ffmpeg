# Compile ffmpeg with decklink

- Install decklink driver driver version 14+ and download sdk version 12.+

https://www.blackmagicdesign.com/support/family/capture-and-playback



```sh
unzip Blackmagic_DeckLink_SDK_*.zip

mkdir -p ~/ffmpeg_sources ~/bin

cp -r Blackmagic\ DeckLink\ SDK\ */Linux/ ~/ffmpeg_sources/BMD_SDK

sudo apt-get update -qq && sudo apt-get -y install \
  autoconf \
  automake \
  build-essential \
  cmake \
  git-core \
  libass-dev \
  libfreetype6-dev \
  libtool \
  libvorbis-dev \
  pkg-config \
  texinfo \
  wget \
  zlib1g-dev
sudo apt-get -y install \
  nasm yasm libx264-dev libx265-dev libnuma-dev libvpx-dev \
  libfdk-aac-dev libmp3lame-dev libopus-dev

cd ~/ffmpeg_sources
wget https://github.com/alainpham/FFmpeg/archive/refs/heads/7.1.1-ap.zip
unzip 7.1.1-ap.zip
cd FFmpeg-7.1.1-ap
PATH="$HOME/bin:$PATH" PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig" ./configure \
  --prefix="$HOME/ffmpeg_build" \
  --pkg-config-flags="--static" \
  --extra-cflags="-I$HOME/ffmpeg_build/include -I$HOME/ffmpeg_sources/BMD_SDK/include" \
  --extra-ldflags="-L$HOME/ffmpeg_build/lib" \
  --extra-libs="-lpthread -lm" \
  --bindir="$HOME/bin" \
  --enable-gpl \
  --enable-libass \
  --enable-libfdk-aac \
  --enable-libfreetype \
  --enable-libmp3lame \
  --enable-libopus \
  --enable-libvorbis \
  --enable-libvpx \
  --enable-libx264 \
  --enable-libx265 \
  --enable-nonfree \
  --enable-decklink

PATH="$HOME/bin:$PATH" make -j `nproc`

```
