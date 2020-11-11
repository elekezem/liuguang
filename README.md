# 鎏光云游戏引擎

## 1. Requirements

### Server

| Target | Minimum | Recommended |
| --- | --- | --- |
| System | Windows 7, 8, 8.1, 10 | Windows 10 |
| GPU | NVDIA GPU | RTX 1080Ti, GTX 2070S tested |
| Software | GeForce Experience | latest GeForce Experience |
| Driver | GeForce Game Ready Driver | latest GeForce Game Ready Driver |

* If you don't have an NVDIA GPU, select --enable-nvenc=false to run `cge`.

### Client

| Target | Minimum | Recommended |
| --- | --- | --- |
| System | Windows 7, 8, 8.1, 10 | Windows 10 |
| CPU | ANY | amd64 |

## 2. Basic

![Data Flow](doc/cg.png)

## 3. QuickStart

All the componets listed below, will be open source by time.

### cge

The `Cloud Gaming Engine`. A precompiled binary is [here](https://ks3-cn-beijing.ksyun.com/liuguang/cge.7z).

You can launch `cge` directly, which will apply the following options:

```
audio-bitrate: 128000
audio-codec: libopus
bind-address: ::
control-port: 8080
enable-nvenc: true
stream-port: 8080
video-bitrate: 1000000
video-gop: 180
video-preset: llhp
video-quality: 23
```

Run `cge --help` to see all options:

```
Usage:
  -h [ --help ]                  produce help message
  --audio-bitrate arg (=128000)  set audio bitrate
  --audio-codec arg (=libopus)   set audio codec, can be one of {libopus, opus,
                                 aac}
  --bind-address arg (=::)       set bind address for listening, eg: 0.0.0.0
  --control-port arg (=8080)     set the UDP port for control flow
  --enable-nvenc arg (=1)        Enable nvenc
  --stream-port arg (=8080)      set the websocket port for streaming, if port
                                 is 0, disable stream out via network. Capture
                                 and encode picture directly at startup but not
                                 on connection establishing, and never stop
                                 this until cge exit. stream port is not same
                                 as control port, this port is only for media
                                 output.
  --video-bitrate arg (=1000000) set video bitrate
  --video-gop arg (=180)         set video gop
  --video-preset arg
  --video-quality arg (=23)      set video quality, lower is better, available
                                 range is 0-51, 0 is lossless
```

You can press `Ctrl+C` to stop it gracefully.

### cgh

Some hook dlls for capturing pictures from D3D game.

Download [here](https://ks3-cn-beijing.ksyun.com/liuguang/cgh.7z)

### cgi

A tool for injecting hook dll into game process.

![Hook game](doc/cgi.png)

```
Allowed options:
  -h [ --help ]              Produce help message
  -d [ --dynamic ] arg       Use dynamic injecting
  -e [ --exec ] arg          Path of the executable
  -a [ --arg ] arg           Arguments of the executable
  -c [ --cd ] arg            Current directory for the executable
  -i [ --imagename ] arg     Image name of the process being injected.
  -w [ --wait ] arg (=1,000) Wait before injecting. unit: ms
  --lx86 arg                 Path of x86 library path
  --lx64 arg                 Path of x64 library path
```

### cgvhid

Cloud gameing Virtual HID driver. For replaying controller event on server.

![Hook game](doc/cgvhid.png)

### cgvidd

Cloud gameing Virtual Indirect Display Driver. For capturing screen on server.

### video_source

A tool for testing. Download [here](https://ks3-cn-beijing.ksyun.com/liuguang/video_source.7z). Just run it, and it will generate simple pictures and write them as video frames to shared memory, then notify `cge` to fetch. You can use `cgc` to see these pictures.

### cgc

A testing version client to work with `cge`. Download [here](https://ks3-cn-beijing.ksyun.com/liuguang/cgc.7z).

```
Usage:
  --server (=127.0.0.1) set server address
  --port (=8080)        set server port
  --hw_decode           hardware decoder, only support d3d11va now.
```

### cgs

A WebRTC server works together with `cge`, to serve web clients.

## 4. Setting Up Your Build Environment

### 4.1 VS2019

Install VS2019 with ATL, CLang.

### 4.2 Boost

Install [Boost](https://www.boost.org/) and set `BOOST_ROOT` environment variable to install directory. [Details](https://blog.umu618.com/2020/09/11/umutech-boost-1-installation/)

### 4.3 FFmpeg

Put [FFmpeg](https://www.ffmpeg.org/download.html) header files in `deps\include`, and the libs in `deps\lib`.

### 4.4 SDL

Put [SDL2](https://www.libsdl.org/) header files in `deps\include`, and the libs in `deps\lib`.

The same as [SDL_ttf 2.0](https://www.libsdl.org/projects/SDL_ttf/) and [SDL_net 2.0](https://www.libsdl.org/projects/SDL_net/).

### 4.5 WDK

Required by cgvhid, cgvidd.

### Building and Testing

Open each solution file with VS, and press F7.

Testing steps:

- Run `cge` on server.

- Run `video_source` on server.

- Run `cgc --server=<server_address>` on anther PC as long as it can access the server over the network.

Test with games:

Assume you want to test USF4.

- Run `cge` on server.

- Run `cgi -d true -e SSFIV.exe -i SSFIV.exe --lx86 .\d3d9yuv.dll` on server.

- Run `cgc --server=<server_address>` on anther PC as long as it can access the server over the network.

Note that `cgvhid` is not ready at this moment, so you can't control the game through `cgc`. `cgvhid` will ready next week, and `cge` will be updated to invoke the driver.
