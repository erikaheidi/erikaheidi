---
title: How to Use OBS Studio to Record or Stream Live Presentations
published: true
description: OBS is a powerful open source video streaming and recording software, available for Linux, macOS and Windows. In this tutorial you'll learn how to set up OBS and use it for streaming and recording live presentations.
tags: beginners, tutorial, streaming, opensource
cover_image: https://dev-to-uploads.s3.amazonaws.com/i/6jge8171amioyne3lppu.png
canonical_url: https://eheidi.dev/blog/how-to-use-obs-studio-to-record-or-stream-live-presentations-474j
---

With quarantine measures and social distancing taking place all over the world, this is definitely the season for online conferences and virtual events. There's also a lot of folks getting into streaming these days, or at least trying to do so - for beginners, streaming can seem difficult, and some streaming tools can definitely look intimidating at a first sight.

[OBS](https://obsproject.com/) (Open Broadcaster Software) is a powerful open source video streaming and recording software, available for Linux, macOS and Windows. Even though OBS is one of the best alternatives for live streaming today, it might not look very intuitive for those who don't have a previous experience with video editing.

In this tutorial targeted at beginners, you'll learn how to set up OBS and use it for recording live presentations and / or streaming directly to Twitch and other platforms.

## Getting Started with OBS

![OBS interface - stream preview, scenes, sources, and controls](https://dev-to-uploads.s3.amazonaws.com/i/1a1w8hbwackdzhl1zmo8.png)

At the center of the screen, you'll se the live preview of your recording / stream. On the bottom left, you have the scenes and sources lists. The audio mixer goes in the middle and on the bottom right you'll see the transition settings and the control buttons for starting / stopping recording and streaming, and to access the settings page.

### Configuring Scenes and Sources

A **scene** is a particular view / way to present: it can contain one or more **sources**. A source can be your webcam, an image, a video, a slideshow, a desktop window, your entire screen, among other things.

Before streaming or recording with OBS, you'll have to configure your **scenes**. You will be able to configure multiple scenes and switch between them while streaming. So, for instance, you might be showing your browser window, and then switch to a scene that is configured to show your terminal window.

![Setting up a scene to show the terminal window](https://dev-to-uploads.s3.amazonaws.com/i/okwfwg99ti88rvofyicd.gif)

You can also make composite scenes with your screen and your webcam, for instance, or multiple application windows. OBS is highly customizable, so you have loads of options to set up your scenes and show exactly what you want to show. 

![Including a webcam in a scene](https://dev-to-uploads.s3.amazonaws.com/i/hr28ente4hgaj6hzan9k.gif)

While streaming, you can switch between scenes by clicking on the scene you want to show next.

![Switching between scenes](https://dev-to-uploads.s3.amazonaws.com/i/1fcnndcs6xugp0d7ca56.gif)

Just be aware that if you set up too many scenes, it might get overwhelming to manage while you are live streaming. It's good to set up a manageable number of scenes and practice a bit to make sure you select the right ones when you're live. The worst that might happen (and happened to me a few times) is forgetting to switch, say, from the browser window to the terminal window, when you want to show some live commands (only realizing your mistake several minutes afterwards). If you're using multiple displays, it's easier to manage the switch between scenes, since you can keep OBS's window visible at all times and thus keep track of what is being streamed.

### Example Scene Setup

![DevOps & Chill Scene Setup](https://dev-to-uploads.s3.amazonaws.com/i/00r6j3qqdyhyz2l7vzcm.png)

When streaming for DevOps & Chill, here's the scenes I have set up:

- **Intro** - this scene uses a slideshow source with two images: a cover image with the title / topic I'll be covering, and another image saying we'll be starting soon. I start the streaming a few minutes before with this slideshow, to give people time to join while making sure they know I'll be starting in a few minutes.
- **Annotations** - this scene uses a window source to show a text editor where I type explanations about what I'm doing - I don't talk in this stream, so I needed a way to include more details about what I'm doing.
- **Browser Window** - this scene is configured to show my browser window.
- **Terminal Window** - this scene is configured to show my Terminal Window.
- **Stream Finished** - I use this scene when the stream is finished. It uses an image source.

I don't use webcam or microphone in this stream. For live talks, however, I'm using a different set of scenes, which then includes a webcam scene and also browser + webcam.

You can create and switch between different scene settings within the menu "Scene Collection". 

![Selecting different scene collections](https://dev-to-uploads.s3.amazonaws.com/i/xcuct3ap5r1mldjasmio.gif)

## Audio and Soundtrack 

OBS allows you to set up multiple audio tracks, so you can have for instance a music playback and audio input from your microphone. However, streaming music involves copyright concerns, and you don't want to get your video offline or muted due to copyright infringements. There are some royalty-free options you can use for streaming, thou. 

[Pretzel.rocks](https://pretzel.rocks) is a royalty-free music service that you can use with Twitch, Youtube and other services.

You can also [download royalty-free music](https://incompetech.com/music/) tracks and create a playlist to play while you're streaming.

In either case, there are two ways to configure audio output for your streaming / recording. You can set these at the profile level, which will make the audio sources available for all the scenes automatically in the current profile, or you can configure audio sources specifically per scene.

To configure audio at the profile level, go to the menu "Settings -> Audio" and then select the appropriate devices you want to enable, and disable the ones you don't need.

![Setting up Audio output](https://dev-to-uploads.s3.amazonaws.com/i/3jtbp4b89kg5f9h40h9s.gif)

For my streams I use the default computer output as audio source, this is easier because anything you play will be streamed as audio - the only problem is that this will also include Google calendar reminders, slack notifications, and any alert sounds that might show up while you're streaming.

You can also set up an "Audio Output Capture" source for a specific scene:

![Setting Up Audio Output as Source](https://dev-to-uploads.s3.amazonaws.com/i/udzhl20g527nqvnialzv.gif)

Once you include audio sources, your "Audio mixer" window will exhibit a live view with the audio levels of all your active audio "tracks".

![audio mixer window](https://dev-to-uploads.s3.amazonaws.com/i/z4cipymqwmerwbg5p1pb.gif)

## Recording Presentations with OBS

With OBS you can record your content without live streaming, which is a great way of preparing pre-recorded live presentations. Because you can also use a video as source, nothing prevents you from pre-recording your stream and then using the recorded video (edited if necessary) as source for a live stream. Most online conferences happening these days are choosing this model over live presentations, because then the speaker can join the live chat and interact / answer questions to the audience while the pre-recorded talk is being streamed.

Recordings are saved in the output directory set in OBS's configuration. This will be set to your home directory by default. To change this location, access the menu "Settings -> Output -> Recording path".
 
![Configuring Output Directory](https://dev-to-uploads.s3.amazonaws.com/i/jf3qu46lc0wx97tlfm5m.gif)

The controls to start / stop recording are located at the bottom right of the screen. Once you start recording, everything you see in the preview window will be recorded.

![Starting and Stopping Recording](https://dev-to-uploads.s3.amazonaws.com/i/ep6oco87apbsmnwemldg.gif)

## Live Streaming with OBS 

In order to be able to stream via OBS, you'll first need to set up your personal streaming key/token in OBS's configuration. You can obtain this information in the streaming platform you'll be using (Twitch, YouTube, etc).

![Configuring streaming service URL](https://dev-to-uploads.s3.amazonaws.com/i/5p1edl40pu40u0sjumv6.gif)

You can set up multiple streaming profiles, this way you can switch between streaming platforms easily. 

![Switching profiles and starting streaming](https://dev-to-uploads.s3.amazonaws.com/i/2k61t8zzfumxec2dngsd.gif)

Once you click the "Start streaming" button, OBS wil start broadcasting your scenes to your streaming service. Everything you see in your preview window will be streamed to your audience.

In your settings, you can configure OBS to start recording automatically when you start a new streaming. This is useful if you plan on editing and/or uploading the video stream to another platform later.

## Bonus: Post Production Tips & Tricks with FFMPEG

Live streaming is fun and exciting, but being able to post the recorded video to your Youtube channel and other platforms is a valuable way to maximize your content reach. A tool like [FFMPEG](https://www.ffmpeg.org/) can help with some batch operations and simple editions that don't require a fully fledged video editor such as [OpenShot](https://www.openshot.org/) (my personal favorite). Here's some tricks I've learned with `ffmpeg` that could be useful for you too:

### Converting to MP4

Converting to different formats is quite straightforward with `ffmpeg`:

```bash
ffmpeg -i input_video.mkv output.mp4
```

### Speeding Up Videos

On DevOps & Chill, I don't talk, I only type. I guess it's not a big deal while it's happening live, but I find the resulting video just really slow, so I like to speed up the videos to 2x the original speed. This is how you do it with `ffmpeg`:

```bash
ffmpeg -i input_video.mkv -an -filter:v "setpts=0.5*PTS" output.mkv
```

This will remove the audio from the video (`-an` option). Removing the audio is necessary because when you speed up without changing the audio, the FPS won't match anymore, and this can cause trouble during playback. And you probably don't want to speed up the audio too ;) See the next tips for how to deal with audio.

To speed up all videos in a directory, you can use this one-liner shell script:

```bash
for i in *.mkv; do ffmpeg -i "$i" -an -filter:v "setpts=0.5*PTS" "${i%.*}_fast.mkv"; done
```

### Extracting Audio from Video

This will extract the audio track from the video and save it to a `.aac` file.

```bash
ffmpeg -i input_video.mkv -vn -acodec copy output_audio.aac
```

The resulting `.aac` file can then be converted to `.mp3` if you wish:

```bash
ffmpeg -i output_audio.aac output_audio.mp3
```


### Generate a playlist with mp3 files

This will concatenate the listed `.mp3` files into one audio file.

```bash
ffmpeg -i "concat:song01.mp3|song02.mp3|song03.mp3|song04.mp3" -acodec copy video_playlist.mp3
```

### Include a new audio track in a video

This will insert the `video_playlist.mp3` audio track in the video.

```bash
ffmpeg -i input_video.mkv -i video_playlist.mp3 -c copy -map 0:v:0 -map 1:a:0 input_video_with_music.mkv        
```


## Conclusion

OBS Studio is a powerful, free and open source software for recording and streaming live presentations. Once you get familiarized with its interface and how to configure scenes with sources, you'll see it's not difficult to use it, and it will enable you to perform professional live streaming and video presentations using only your computer.

There are certainly many different ways to configure OBS to suit your needs, depending on what kind of content you want to present. Play around with different settings and experiment until you find the scenes that work best for you! I hope you'll now feel empowered to start that live streaming idea you've been postponing :) what time better than now?


