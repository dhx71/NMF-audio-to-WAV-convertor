# extract.py description

extract.py simply extract audio streams from the .nmf file and save them into a file named `<original-filename>_stream<n>.<format>`. Ex.: `7166083243371987119.nmf` => `7166083243371987119_stream0.g729`

The extracted files occupy a bit less space on the disk. Later when you want to listen to them, you can convert the files to WAV as follow:

    ffmpeg -y -i 7166083243371987119_stream0.g729 7166083243371987119_stream0.wav

# Description

This is an improved `NMF` to `WAV` converter based on original code by `@quarckster` at: [https://github.com/quarckster/nmf_to_wav](https://github.com/quarckster/nmf_to_wav)

It converts multi-track NMF audio files to individual WAV stream audio files.

In this update we resolved the following 4 bugs/issues we encountered:

## Bug 1: `get_compression_type()` data unpack indexing issue

While looping through data to determine the compression type in `get_compression_type()` the data struct itself is updated in the loop causing the index i to no longer be an absolute index on the original data struct, but rather the updated one, causing the index to shift outside of bounds when the chunk size is larger than 22 bytes. In our case we encountered chunksizes 6 times as large as that (132).

## Bug 2: `FFMPEG -f` requires formats instead of codecs

The -f parameter in the most recent ffmpeg version as of writing (4.2.1) takes slightly different values for its -f parameter (force format). In the original code the -f parameter was actually used incorrectly with a codec value, as opposed to a format value. If you perform a "ffmpeg -formats" you see that those values are different from "ffmpeg -codecs". So now the -f parameter is fed actual "formats" correpsonding to the original codec list

## Bug 3: Null-check of compression value

In combination with Bug 2 added a "null-check" on "compression" to default to "g729" instead of have an empty -f parameter value

## Bug 4: Added `packet_types` and `sub_types`

We had to consume and extract more packet headers than the original packet type 4 and subtype 0 to avoid getting 0kb or 1kb WAVs as output with no sound. So, we added packet_type 4 and sub_type 3, as well as packet_type 5 and sub_type 300. This has been determined by trial and error and may still need improvement

# Requirements

Tested with:

- **Python 3.7.3** [https://www.python.org/downloads/](https://www.python.org/downloads/)
- **FFMPEG 4.2.1 (for Windows - because the codec DLLs are Windows based)** [https://ffmpeg.zeranoe.com/builds/win32/shared/ffmpeg-4.2.1-win32-shared.zip](https://ffmpeg.zeranoe.com/builds/win32/shared/ffmpeg-4.2.1-win32-shared.zip)
- **FFMPEG libs** `swscale-5.dll`, `swresample-3.dll`, `postproc-55.dll`, `avcodec-58.dll`, `avdevice-58.dll`, `avfilter-7.dll`, `avformat-58.dll` and `avutil-56.dll` present in the FFMPEG folder.
- **NICE Player Codec Pack** installed that can be downloaded from the NICE Software Download Center in your NICE customer portal.

# Background Info

This is an improved NMF to WAV converter based on original code by `@quarckster` at: [https://github.com/quarckster/nmf_to_wav](https://github.com/quarckster/nmf_to_wav). It processes `NMF` files that are used by Nice Audio Player and generated by Nice Call Center software.

Using the Python struct module the script parses raw audio data and pipes it to `FFMPEG` to output to WAV.

# Usage:

`python nmf_converter.py path_to_nmf_file`

## Contributors

| Roles       | Author(s)                       |
| ----------- | ------------------------------- |
| Lab Manuals | Manfred Wittenbols @mwittenbols |

## Version history

| Version | Date        | Comments        |
| ------- | ----------- | --------------- |
| 1.0     | Oct 9, 2019 | Initial release |

## Disclaimer

**THIS CODE IS PROVIDED _AS IS_ WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING ANY IMPLIED WARRANTIES OF FITNESS FOR A PARTICULAR PURPOSE, MERCHANTABILITY, OR NON-INFRINGEMENT.**
