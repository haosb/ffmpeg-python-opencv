# ffmpeg-python-opencv
FFmpeg 4.4 compiled with configuration for better RTSP support + Python 3.8 + OpenCV 4.5.4.60.

#### Why

This image created to read all RTSP streams (with beer cams), ready to use for developers.
Image contains pip3 packages: opencv-python==4.5.4.60 ffmpeg-python==0.2.0.

#### Dockerhub (image)
[link](https://hub.docker.com/r/haoshand/ffmpeg-python)

#### Dockerfile
```Dockerfile
FROM debian:stable-slim as build-stage

ARG DEBIAN_FRONTEND=noninteractive

RUN apt update && \
        apt install -y git make nasm yasm pkg-config \
        gcc libx264-dev libxext-dev libxfixes-dev zlib1g-dev \
        libgl1 libsm-dev libxcb-util-dev libxcb-xinerama0

WORKDIR /app
COPY FFmpeg /app/FFmpeg # This from git clone https://github.com/FFmpeg/FFmpeg -b release/4.4

WORKDIR /app/FFmpeg
RUN ./configure --enable-nonfree --enable-gpl --enable-libx264 --enable-muxer=mpeg2video \
                --enable-muxer=h264 --enable-muxer=hevc --enable-muxer=mp4 --enable-decoder=hevc \
                --enable-decoder=h264 --enable-zlib --enable-network --enable-protocol=tcp \
                --enable-protocol=udp --enable-protocol=rtp --enable-demuxer=rtsp --enable-libxcb
RUN make && make install



FROM python:3.8.16-slim-bullseye

ENV PYTHONUNBUFFERED=1

RUN pip3 install opencv-python==4.5.4.60 ffmpeg-python==0.2.0

COPY --from=build-stage /lib/x86_64-linux-gnu /lib/x86_64-linux-gnu
COPY --from=build-stage /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu
COPY --from=build-stage /usr/local/bin/ /usr/bin/
```
