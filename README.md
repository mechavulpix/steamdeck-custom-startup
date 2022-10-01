# @mechavulpix Custom SteamDeck Startups
My personal collection of custom startup animations for the SteamDeck.
Videos created by me.
Script and startup config/installation forked from (steamdeck_startup_animations)[https://github.com/kageurufu/steamdeck_startup_animations]

# From the Original README.md:
## Installation

```sh
mkdir -p "$HOME/homebrew"
mkdir -p "$HOME/.config/systemd/user"
git clone https://github.com/mechavulpix/steamdeck-custom-startup.git "$HOME/homebrew/startup_animations"
ln -sf "$HOME/homebrew/startup_animations/randomize_deck_startup.service" "$HOME/.config/systemd/user/randomize_deck_startup.service"
systemctl --user daemon-reload
systemctl --user enable --now randomize_deck_startup.service
```

## Uninstallation

```sh
bash $HOME/homebrew/startup_animations/uninstall.sh
```

### Creating the animation

```sh
# Convert the video from whatever input formats to a webm video in VP9 encoding, with vorbis encoded audio
ffmpeg -i ps1.webm -i ps1.m4a \
       -map 0:v:0  -map 1:a:0 \
       -filter:v "scale='min(1280,iw)':min'(800,ih)':force_original_aspect_ratio=decrease,pad=1280:800:(ow-iw)/2:(oh-ih)/2" \
       -c:v vp9 \
       -c:a libvorbis \
       my_deck_startup_ps1.webm

# Make sure the generated file is less than 1840847B here
# Mine was `389670`, plenty small enough
stat -c '%s' my_deck_startup_ps1.webm

truncate -s 1840847 my_deck_startup_ps1.webm
```

The ffmpeg command is a bit confusing, so heres a breakdown

* `-i ps1.webm -i ps1.m4a`  
  Load both the video and audio files we downloaded.
* `-map 0:v:0  -map 1:a:0`  
  Only use the first video stream from the first file, and first audio stream from the second.  
  This prevents having multiple video or audio streams in the final file
* `-filter:v "scale='min(1280,iw)':min'(800,ih)':force_original_aspect_ratio=decrease,pad=1280:800:(ow-iw)/2:(oh-ih)/2"`  
  This is most confusing part, basically we're resizing the video to fit 1280x800  
  `min'(1280,iw)':min'(800,ih)'` ensures the target size is never upscaled  
  `force_original_aspect_ratio=decrease` scales to fit within a given size, keeping the original aspect ratio  
  `pad=1280:800:(ow-iw)/2:(oh-ih)/2` pad the video size to 1280:800, centering the original video. This is optional and I might not use it in the future
* `-c:v vp9 c:a libvorbis` Select our output VP9 / vorbis codecs

 
