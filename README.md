# 🎥 SRT Live

Low latency live-streaming server based on the Secure Reliable Transport (SRT) protocol. Forked from [edward-wu/srt-live-server](https://github.com/Edward-Wu/srt-live-server) (SLS).

## Introduction

srt-live-server (SLS) is an open source live streaming server for low latency based on Secure Reliable Tranport(SRT).
Normally, the latency of transport by SLS is less than 1 second in internet.


## Requirements

Please install the [SRT](https://github.com/Haivision/srt) first. SLS can only run on a linux-based OS - such as macOS, Centos or Ubuntu.

## Compile

`$ sudo make`

bin file is generated in subdir of 'bin'.

## Directivies

Please see the directives [wiki](https://github.com/Edward-Wu/srt-live-server/wiki/Directives) page.

## Usage

`$ cd bin`

### Help information

`$ ./sls -h`

### Run with default config file

`$ ./sls -c ../sls.conf`

## Testing

SLS only supports the MPEG-TS format streaming. 

### ffmpeg

You can push camera live stream by FFMPEG.Please download ffmpeg sourcecode from https://github.com/FFmpeg/FFmpeg, then compile FFMPEG with --enable-libsrt. 

srt library is installed in folder /usr/local/lib64.
if "ERROR: srt >= 1.3.0 not found using pkg-config" occured when compiling FFMPEG, please check the ffbuild/config.log file and follow its instruction to resolve this issue. in most cases it can be resolved by the following command:
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:/usr/local/lib64/pkgconfig

if "error while loading shared libraries: libsrt.so.1" occured, please add srt library path to file '/etc/ld.so.conf' as the default path, then refresh by comand /sbin/ldconfig with root.

use ffmpeg to push camera stream with SRT(on my mac):

`$ ./ffmpeg -f avfoundation -framerate 30 -i "0:0" -vcodec libx264  -preset ultrafast -tune zerolatency -flags2 local_header  -acodec libmp3lame -g  30 -pkt_size 1316 -flush_packets 0 -f mpegts "srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test"`

Play the SRT stream with ffplay:

`$ ./ffplay -fflags nobuffer -i "srt://[your.sls.ip]:8080?streamid=live.sls.com/live/test"`

### OBS

OBS (>= v25) supports the SRT protocol. You can use the following test url: `srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test`

### srt-live (client)

There is a test tool in SLS, which can be used performance test because of no codec overhead but main network overhead. the slc can play a srt stream to a ts file, or push a ts file to a srt stream.

#### Push ts file as srt url

`$ cd bin`

`$ ./slc -r srt://[your.sls.ip]:8080?streamid=uplive.sls.com/live/test -i [the full file name of exist ts file]`

#### Play srt url

./slc -r srt://[your.sls.ip]:8080?streamid=live.sls.com/live/test -o [the full file name of ts file to save]

### Docker

Please refer to [srt-live-docker](https://github.com/superepicstudios/srt-live-docker)

## Notes

- SLS refer to the RTMP url format(domain/app/stream_name), example: www.sls.com/live/test. The url of SLS must be set in streamid parameter of SRT, which will be the unique identification a stream.
- How to distinguish the publisher and player of the same stream? In conf file, you can set parameters of domain_player/domain_publisher and app_player/app_publisher to resolve it. Importantly, the two combination strings of domain_publisher/app_publisher and domain_player/app_player must not be equal in the same server block.
- I supply a simple android app for test sls, your can download from https://github.com/Edward-Wu/liteplayer-srt
