---
title: "How to Encode X265 With Ffmpeg"
date: 2021-09-07T13:02:08Z
draft: true
---

FFmpeg supports encoding HEVC/H.265 since 2014-2-12 with libx265. Now you can use ffmpeg directly to encode HEVC or use another encoder then manually mux that into .mp4, .mov, or .mkv using FFmpeg.

Available Encoders
Current H.265/HEVC encoders are:

MulticoreWare x265
Chinese x265
DivX HEVC Encoder
Kvazaar
Only the MulticoreWare and DivX are suggested because of their development time and reliability.

Two x265's
One confusion is that there are two independent projects both named "x265", one by a Chinese college student and is practically dead, and another by a commercial company called MulticoreWare. Although the former started out first, it is practically dead now, and the latter is under active development and is endorsed by VideoLAN (the developer of x264).

Timeline of FFmpeg HEVC Encoding Support
2013-10-12 .mov muxing support added in 53f903b.
2014-02-12 libx265 encoding support added in bb6b173.
2014-03-09 More complete Matroska muxing support for HEVC added in 38aee64.
2014-03-09 More complete .mov and new .mp4 muxing support added in 9563e67.
tl;dr
Anyways, these are a complete set of current possibilities of encoding HEVC in order of my recommendation:

Directly using MulticoreWare libx265 with FFmpeg. This means with the latest Zeranoe build you can now do this:

ffmpeg -i INPUT -c:v libx265 -an -x265-params crf=25 OUT.mov
# With audio
ffmpeg -i INPUT -c:v libx265 -c:a copy -x265-params crf=25 OUT.mov
Note: FFmpeg builds newer than 2014-03-09 allows you to mux HEVC into .mp4 files now (commit 9563e67):

ffmpeg -i INPUT -c:v libx265 -an -x265-params crf=25 OUT.mp4
You can also mux it in Matroska (.mkv):

ffmpeg -i INPUT -c:v libx265 -an -x265-params crf=25 OUT.mkv
Using standalone MulticoreWare x265 and then mux the resulting file with FFmpeg or MP4Box. This will work with older FFmpeg builds that don't have libx265 support.

# Decode input using FFmpeg and encode using x265 using pipe
ffmpeg -i INPUT -f yuv4mpegpipe -pix_fmt yuv420p - | \
x265 --y4m -o encoded.265 -

# Mux the resulting encoded .265 file into an .mp4 or .mov

# Using ffmpeg: only works with newer builds that support muxing HEVC
# Muxing to .mp4 supported since 03-09-2014
ffmpeg -i encoded.265 -c copy out.mp4
# With audio
ffmpeg -i encoded.265 -i INPUT -map 0 -map 1:a -c copy out.mp4

# Using MP4Box
MP4Box -add raw.265 out.mp4
# With audio
MP4Box -add {INPUT}#audio -add raw.265 out.mp4
Thanks to @Duvrai.

Using DivX HEVC Encoder and then mux it.

The way of using it is similar to using standalone x265 (see above).

Update: FFmpeg has HEVC encoding support using MulticoreWare libx265 now.

Update 2: FFmpeg now has support for muxing HEVC into mp4 AND mov now