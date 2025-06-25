# Compile ffmpeg with decklink

- Install decklink driver driver version 12+ and download sdk version 12.+
- works on ubuntu:noble and debian:bookworm

https://www.blackmagicdesign.com/support/family/capture-and-playback


docker container

```sh
docker run --name bmg -e LANG=C.UTF-8 -dit debian:bookworm 
docker run --name bmg -e LANG=C.UTF-8 -dit ubuntu:noble
```

```sh
# if debian
cat <<EOF > /etc/apt/sources.list
deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://deb.debian.org/debian-security/ bookworm-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware
EOF

rm /etc/apt/sources.list.d/debian.sources
# end if debian

apt update -y && apt upgrade -y
apt install -y wget curl unzip

cd

wget http://192.168.8.100:28000/blackmagic/Blackmagic_DeckLink_SDK_12.9.zip
wget http://192.168.8.100:28000/blackmagic/Blackmagic_Desktop_Video_Linux_12.9.tar

tar xvf Blackmagic_Desktop_Video_Linux_12.9.tar

apt install -y /root/Blackmagic_Desktop_Video_Linux_12.9/deb/x86_64/desktopvideo_12.9a3_amd64.deb

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
  libfdk-aac-dev libmp3lame-dev libopus-dev libgpac-dev libsdl1.2-dev libtheora-dev libva-dev libvdpau-dev libxcb1-dev libxcb-shm0-dev libxcb-xfixes0-dev texi2html nvidia-cuda-toolkit

echo "Installing the nVidia NVENC SDK."
cd ~/ffmpeg_sources
git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git -b master --single-branch --depth=1
cd nv-codec-headers
make
make install

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
  --enable-cuda-nvcc \
  --enable-cuvid \
  --enable-libnpp \
  --extra-cflags="-I/usr/local/cuda/include/" \
  --extra-ldflags=-L/usr/local/cuda/lib64/ \
  --enable-gpl \
  --enable-libass \
  --enable-libfdk-aac \
  --enable-vaapi \
  --enable-libfreetype \
  --enable-libmp3lame \
  --enable-libopus \
  --enable-libtheora \
  --enable-libvorbis \
  --enable-libvpx \
  --enable-libx264 \
  --enable-libx265 \
  --enable-nonfree \
  --enable-nvenc \
  --enable-decklink  \
  --enable-ffplay
 
PATH="$HOME/bin:$PATH" make -j `nproc`


```

copy to server

```sh
export distrib=ubuntu

scp ffmpeg  apham@192.168.8.100:/home/apham/apps/static/data/ffmpeg/$distrib
scp ffprobe apham@192.168.8.100:/home/apham/apps/static/data/ffmpeg/$distrib
scp ffplay  apham@192.168.8.100:/home/apham/apps/static/data/ffmpeg/$distrib
```