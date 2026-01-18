FROM ubuntu:noble

WORKDIR /root

RUN apt update -y && \
    apt upgrade -y && \
    apt install -y wget curl unzip && \
    apt install -y \
                libgl1 \
                dkms \
                linux-headers-generic

RUN wget http://192.168.8.100:28000/blackmagic/Blackmagic_DeckLink_SDK_12.9.zip && \
    wget http://192.168.8.100:28000/blackmagic/Blackmagic_Desktop_Video_Linux_12.9-patched.tar && \
    tar xvf Blackmagic_Desktop_Video_Linux_12.9-patched.tar && \
    dpkg -i /root/Blackmagic_Desktop_Video_Linux_12.9/deb/x86_64/desktopvideo_12.9a3_amd64.deb && apt -f install && \
    unzip Blackmagic_DeckLink_SDK_*.zip

RUN mkdir -p ~/ffmpeg_sources ~/bin && \
    cp -r Blackmagic\ DeckLink\ SDK\ */Linux/ ~/ffmpeg_sources/BMD_SDK

RUN apt -y install \
  autoconf \
  automake \
  build-essential \
  cmake \
  git-core \
  libass-dev \
  libfreetype6-dev \
  libgnutls28-dev \
  libmp3lame-dev \
  libsdl2-dev \
  libtool \
  libva-dev \
  libvdpau-dev \
  libvorbis-dev \
  libxcb1-dev \
  libxcb-shm0-dev \
  libxcb-xfixes0-dev \
  meson \
  ninja-build \
  pkg-config \
  texinfo \
  wget \
  yasm \
  zlib1g-dev

RUN apt -y install \
  nasm yasm libx264-dev libx265-dev libnuma-dev libvpx-dev \
  libfdk-aac-dev libmp3lame-dev libopus-dev libgpac-dev libsdl1.2-dev \
  libasound2-dev texi2html

RUN cd ~/ffmpeg_sources && \
    wget https://github.com/alainpham/FFmpeg/archive/refs/heads/7.1.1-ap.zip && \
    unzip 7.1.1-ap.zip && \
    cd FFmpeg-7.1.1-ap && \
    PATH="$HOME/bin:$PATH" PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig" ./configure \
        --prefix="$HOME/ffmpeg_build" \
        --pkg-config-flags="--static" \
        --extra-cflags="-I$HOME/ffmpeg_build/include -I$HOME/ffmpeg_sources/BMD_SDK/include" \
        --extra-ldflags="-L$HOME/ffmpeg_build/lib" \
        --extra-libs="-lpthread -lm" \
        --bindir="$HOME/bin" \
        --enable-gpl \
        \
        \
        --enable-libass \
        --enable-libfdk-aac \
        --enable-libfreetype \
        --enable-libmp3lame \
        --enable-libopus \
        \
        --enable-libvorbis \
        --enable-libvpx \
        --enable-libx264 \
        --enable-libx265 \
        --enable-nonfree \
        \
        --enable-vaapi \
        --enable-libpulse \
        --enable-decklink  \
        --enable-ffplay && \
    PATH="$HOME/bin:$PATH" make -j `nproc` install

ENTRYPOINT  ["/root/bin/ffmpeg"]
