# aidatasetfromvideo

A utility to sort and slice a captioned video into a dataset for [tacotron](https://github.com/keithito/tacotron)

[![GitHub all releases](https://img.shields.io/github/downloads/hexcodefff/aidatasetfromvideo/total)](https://github.com/HexCodeFFF/aidatasetfromvideo/releases)
![GitHub package.json version](https://img.shields.io/github/package-json/v/hexcodefff/aidatasetfromvideo)
![Total Lines](https://img.shields.io/tokei/lines/github/HexCodeFFF/aidatasetfromvideo)
[![stars](https://img.shields.io/github/stars/HexCodeFFF/aidatasetfromvideo?style=social)](https://github.com/HexCodeFFF/aidatasetfromvideo/stargazers)
![built with immense swag](https://img.shields.io/static/v1?label=built+with&message=pain&color=e74c3c)

## premise

Creating a dataset for an AI voice is typically tedious work. Audio needs to be manually sliced and transcribed. This is
compounded by the fact that typically 30 minutes or more of voice data is needed.

This project takes advantage of the work of captioners. Essentially all content that is aired on television has already
been captioned. Caption data is already pre-sliced and transcribed, however, it isn't often perfect as-is for AI. This
project plays videos caption-by-caption to allow the user to discard lines that aren't good and to sort lines by _who_
is saying them.

# usage

## installation

Download the latest installer from [the releases page](https://github.com/HexCodeFFF/aidatasetfromvideo/releases). The
installer should install the program, add a desktop icon, and then launch it. No input required.

## on launch

![upload](screenshots/main.png)
currently the app has two functions.
[the main function is to create a dataset from a video file.](#the-main-app)
[the other is to combine existing datasets.](#merging-datasets)

## the main app

### choose a file

![upload](screenshots/upload.png)

The app presents you with a "Choose a file" screen. The file _must_ have video, audio, and subtitles.

To add subtitles to an MKV file:

```shell
ffmpeg -i infile.mkv -i infile.srt -c copy outfile.mkv
```

To add subtitles to an MP4 file:

```shell
ffmpeg -i infile.mp4 -i infile.srt -c copy -c:s mov_text outfile.mp4
```

MP4 files are less flexible than MKV and only support one type of caption format.

### once you've chosen a file

![app](screenshots/app.png)

The app will begin to play the video subtitle-by-subtitle.

The subtitles are automatically cleaned up to aid assignment. All text in brackets (i.e. `[screams]`) is removed, as
well as some other typical things in subtitles (dashes, html tags, excessive newlines). If the program removes actual
dialog, there is a "revert to unfiltered" option when clicking "Revert Text". To accompany this, there is an "Edit Text"
button to manually modify the subtitle.

Each subtitle can be assigned to a "character". Each character will have their own dataset once the voice lines are
exported. Voice lines must be assigned to a character if you wish for them to be exported.

pro-tip: clicking on the video will replay the current subtitle.

### results

The results are created when the export data button is clicked or all captions have been sorted. The output will be in
the installation directory, typically `C:\Users\<user>\AppData\Local\aidatasetfromvideo`. The program will automatically
open the folder once generated.

#### format of result

```
installdir
  |- datasets-* (number after "datasets-" will be first available, no datasets are overwritten)
      |- meta.txt
      |- char-0
          |- list.txt
          |- wavs
              |- 0.wav
              |- 1.wav
              | ... (1 wav per voiceline)
      |- char-1 
      | ... (1 char folder per character)
```

#### format of list.txt

```
0.wav|<transcript of 0.wav>
1.wav|<transcript of 1.wav>
... (1 line per wav)
```

Each `char-*` folder should be a [tacotron](https://github.com/keithito/tacotron) compatible database. The generated wav
files are 16-bit signed PCM little-endian (FFmpeg's [`pcm_s16le`](https://trac.ffmpeg.org/wiki/audio%20types)), in order
to comply with tacotron's requirements.

#### format of meta.txt

```
datasets generated from <videofile>:
char-0: "<name of character #0>" (<seconds of data>s; <lines of data> line(s))
char-1: "<name of character #1>" (<seconds of data>s; <lines of data> line(s))
... (1 line per character)

Generated by aidatasetfromvideo https://github.com/HexCodeFFF/aidatasetfromvideo
```

`meta.txt` is a human-readable file giving a basic overview of the data contained within the folder. it is not designed
to be machine-readable nor does tacotron use it.

## merging datasets

each time a video is finished within the application, the subtitle data is packaged into one tacotron dataset. this
could be a problem if your data is spread across multiple videos (such as an episodic tv show).

![upload](screenshots/combine.png)

this allows you to select (by drag-and-drop or upload) two or more datasets and combine them into a singular dataset.


# TODO

- make it more clear that clicking the video replays the subtitle
- allow upload of a video file, and an SRT/caption file separately
- add support for more dataset formats for other AI voice models
- reduce file size
  - maybe use pnpm or webpack?
- support linux?
