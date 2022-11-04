# ytcli-guide

This repository is intended to provide a consolidated guide for accessing YouTube from the command line on linux.

# Programs

* [`ytfzf`](https://github.com/pystardust/ytfzf) - Searches YouTube.
* [`fzf`](https://github.com/junegunn/fzf) - Default menu for selecting videos with ytfzf. [`dmenu`](https://tools.suckless.org/dmenu/) can also be used but some features will be missing. 
* [`ueberzug`](https://github.com/seebye/ueberzug) - Generates thumbnails in the terminal. X11 only. See ytfzf page for wayland alternatives.
* [`mpv`](https://github.com/mpv-player/mpv) - Default Media Player.
* [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) - For downloading videos.

You should already have these, but just in case:
* [`ffmpeg`](https://github.com/FFmpeg/FFmpeg) - For playing network streams.
* [`wget`](https://github.com/mirror/wget) - For making scrapes.

# Scripts for mpv

Scripts should be placed in `~/.config/mpv/scripts`.

Scripts often have their own configs and customisation options, so make sure to check for them. These belong in `~/.config/mpv/script-opts`.

A full list of scripts for mpv can be found [here](https://github.com/mpv-player/mpv/wiki/User-Scripts). 

* [`quality-menu`](https://github.com/christoph-heinrich/mpv-quality-menu) - Allows you to select video quality.
* [`youtube-upnext`](https://github.com/cvzi/mpv-youtube-upnext) - Fetches recommended videos for the current video.
* [`reload`](https://github.com/sibwaf/mpv-scripts/blob/master/reload.lua) - Allows you to reload the current video if it freezes. Restarts where you left off.
* [`mpv_sponsorblock`](https://github.com/po5/mpv_sponsorblock) - Automatically skips sponsored ads and other off topic segments in videos. Also adds markers on the osc timeline.
* [`copy-permalink`](https://gist.github.com/2084x/699fe48cff983bcbaf532d82e1515269) - Copies link of current video. Useful with 'com' alias.
* [`auto-keep-gui-open`](https://github.com/VideoPlayerCode/mpv-tools/blob/master/scripts/auto-keep-gui-open.lua) - When a file ends, keeps the gui open if it's a video and closes if it's audio. Useful if you want to scrape recommended videos after a video ends.

Some I don't use but may be of interest:
* [`mpv-youtube-search`](https://github.com/rozari0/mpv-youtube-search) / [`youtube-search`](https://github.com/CogentRedTester/mpv-scripts/blob/master/youtube-search.lua) - Two scripts to search YouTube from inside mpv.
* [`mpv_sponsorblock_minimal`](https://github.com/bbhtt/mpv_sponsorblock_minimal) - Lightweight mpv_sponsorblock. Some features missing.
* [`ytdl-preload`](https://gist.github.com/bitingsock/17d90e3deeb35b5f75e55adb19098f58) - Preloads network streams in the playlist by downloading to a local tmp file. Can be buggy and download audio and video out of sync.
* [`mpv-ytdlAutoFormat`](https://github.com/Samillion/mpv-ytdlautoformat) - Automatically change videos to your desired resolution. (I prefer to set this in my yt-dlp conf).
* [`youtube-download`](https://github.com/cvzi/mpv-youtube-download) - Download current video with a single key press.

# Configuration
## ytfzf
### `~/.config/ytfzf/conf.sh`

This is the main conf file.
```
pages_to_scrape=1
sub_link_count=5
show_thumbnails=1
url_handler_opts="--force-window=yes"
```
#### Notes

* I set FZF_DEFAULT_OPTS in my shell profile. If you don't do this you may want to set them in the ytfzf conf. It could look something like this: `FZF_DEFAULT_OPTS="--layout=reverse -e -m --height=100% --bind=ctrl-a:select-all"`

* Occasionally a video will play in audio only mode, so I like to force a window. If that doesn't bother you, the last line is unnecessary.

### `~/.config/ytfzf/submenu-conf.sh`

This is the conf file for playlist and channel pages.

```
show_thumbnails=1
url_handler_opts="--force-window=yes"
is_loop=1
is_detach=1
enable_back_button=0
```
#### Notes

* I use detach and loop in the ytfzf submenus for two reasons:
	1. I usually want to watch multiple videos from these sources and may not open them all at once.
	2. dwm + swallow patch is buggy with the loop option and you will lose thumbnails if you don't detach.

## yt-dlp
### `~/.config/yt-dlp/config`
```
-o ~/dl/%(title)s.%(ext)s
--sponsorblock-remove default
-S "height:2160"
```
#### Notes

* The options in your yt-dlp config will be respected by mpv when playing network streams. For example, I set my max resolution to be 4K, since anything higher always stutters.

## mpv
### `~/.config/mpv/mpv.conf`

Not going to include my entire config here, just the relevant parts.
```
slang=eng,en # auto select eng subs where possible
reset-on-next-file=pause # prevent video starting paused
```

# Useful shell aliases
`yts="ytfzf --type=all --thumbnail-quality=maxresdefault"` - Make a search with ytfzf. Includes all types of results (channel, playlist and video) and downloads high quality thumbnails.

`ytc="ytfzf --type=channel"` - Search for a channel and browse it's videos.

`com="ytfzf -c comments --skip-thumb-download"` - Scrape comments of a video with ytfzf. Search term should be video link. Does not download channel avatars because they can sometimes cover up long comments.

`sub="ytfzf -c SI --sort --detach -l"` - Scrapes your subscription file and sorts by upload date. Loops since there's likely multiple videos to watch.

# Tips for ytfzf
* You can use tab to select and open multiple videos. First selected video will play and others will be added to the playlist in order.
* ctrl-a (with my fzf settings) will allow you to select and open all videos. Useful for playlists.
* alt-p will scrape the next page of search results.
* ctrl-j/k moves you up and down the list of search results.
* You can add a subscriptions file to scrape a list of channels for their most recent uploads. See the ytfzf manual for how the file should be set up.

# Other stuff of interest
* [`ff2mpv`](https://github.com/woodruffw/ff2mpv) - an extension for firefox and chromium-based browsers that lets you right click links and open them in mpv. [Installation guide](https://youtube.com/watch?v=jfyt5ueyWN8).
* [`invidious`](https://github.com/iv-org/invidious) - light weight, privacy respecting front end for YouTube.
* [`Piped`](https://github.com/TeamPiped/Piped) - light weight, privacy respecting front end for YouTube with many extra features.
* [`HyperPipe`](https://codeberg.org/Hyperpipe/Hyperpipe) - privacy respecting front end for YouTube Music.
* [`NewPipeSponsorBlock`](https://github.com/gilbsgilbs/NewPipeSponsorBlock) - YouTube front end for android with many extra features.
* [`Libredirect`](https://github.com/libredirect/libredirect) - Extension to automatically redirect links to privacy respecting front ends.
