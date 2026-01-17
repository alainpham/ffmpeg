docker build -t ffmpeg .


docker run --rm --device=/dev/blackmagic ffmpeg -sources decklink


docker run --rm -it --entrypoint='bash' --device=/dev/blackmagic ffmpeg