# ytcli-guide

This repository is intended to provide a consolidated guide for accessing YouTube from the command line on linux.

# Programs

* [`ytfzf`](https://github.com/pystardust/ytfzf) - Searches YouTube.
* [`fzf`](https://github.com/junegunn/fzf) - Default menu for selecting videos with ytfzf. [`dmenu`](https://tools.suckless.org/dmenu/) can also be used but some features will be missing. 
* [`ueberzug`](https://github.com/seebye/ueberzug) - Generates thumbnails in the terminal. X11 only. See ytfzf page for wayland alternatives.
* [`mpv`](https://github.com/mpv-player/mpv) - Default Media Player.
* [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) - For downloading videos.
* [`newsboat`](https://github.com/newsboat/newsboat) - For RSS feeds.

You should already have these, but just in case:
* [`ffmpeg`](https://github.com/FFmpeg/FFmpeg)
* [`wget`](https://github.com/mirror/wget)
* [`curl`](https://github.com/curl/curl)

# Scripts for mpv

Scripts should be placed in `~/.config/mpv/scripts`.

Scripts sometimes have their own configs, so make sure to check for them. These belong in `~/.config/mpv/script-opts`.

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

A full list of scripts for mpv can be found [here](https://github.com/mpv-player/mpv/wiki/User-Scripts). 

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

### `~/.config/ytfzf/submenu-conf.sh`

This is the conf file for playlist and channel pages.

```
show_thumbnails=1
url_handler_opts="--force-window=yes"
enable_back_button=0
```

Increase page and sub counts to scrape more videos by default.

Occasionally a video will play in audio only mode, so I like to force a window.

I set FZF_DEFAULT_OPTS in my shell profile. If you don't do this you may want to set them in the ytfzf confs. It could look something like this: `FZF_DEFAULT_OPTS="--layout=reverse -e -m --height=100% --bind=ctrl-a:select-all"`

## yt-dlp
### `~/.config/yt-dlp/config`
```
-o ~/dl/%(title)s.%(ext)s
--sponsorblock-remove default
-S "height:2160"
```

The options in your yt-dlp config will be respected by mpv when playing network streams. For example, I set my max resolution to be 4K, since anything higher always stutters.

## mpv
### `~/.config/mpv/mpv.conf`

Not going to include my entire config here, just the relevant parts.

```
slang=eng,en # auto select eng subs where possible
reset-on-next-file=pause # prevent video starting paused
```

# Useful shell aliases
`yts="ytfzf --type=all --thumbnail-quality=maxresdefault"` - Make a search for all types of results (channel, playlist and video) and download high quality thumbnails.

`ytc="ytfzf --type=channel"` - Search for a channel and browse it's videos.

`com="ytfzf -c comments --skip-thumb-download"` - Scrape comments of a video. Search term should be video link. Does not download channel avatars because they can sometimes cover up long comments.

`sub="ytfzf -c SI --sort"` - Scrape your subscription file and sort by upload date.

# Tips for ytfzf
* You can use tab to select and open multiple videos. First selected video will play and others will be added to the playlist in order.
* ctrl-a (with my fzf settings) will allow you to select and open all videos. Useful for playlists.
* alt-p will scrape the next page of search results.
* ctrl-j/k moves you up and down the list of search results.
* You can add a subscriptions file to scrape a list of channels for their most recent uploads. See the ytfzf manual for how the file should be set up.
* If you use dwm + swallow patch and want to loop menus, add `is_detach=1` to your config, otherwise thumbnails and menus will break when the window is restored.

# RSS
Although you can get subscriptions with ytfzf, it's not the most efficient way to do so for a few reasons:

1. You can only scrape a small number of videos per channel before it becomes excessive.
2. Large subscription files will take a long time to load.
3. You have to manually check for new videos.

Instead we can use RSS to get updates for new videos. Feeds can be viewed in an RSS feed reader such as [`newsboat`](https://github.com/newsboat/newsboat) and be updated automatically in the background. I suggest using Luke Smith's [dot files](https://github.com/LukeSmithxyz/voidrice/blob/master/.config/newsboat/config) and [scripts](https://github.com/LukeSmithxyz/voidrice/blob/master/.local/bin/cron/newsup) to quickly set up newsboat and a cronjob to do updates. There are many other readers you could use, but I like newsboat because it's terminal based and can open videos in mpv with a single key press.

There are two ways to get an RSS feed link for YouTube:

1. Use the built in RSS on [`Invidious`](https://github.com/iv-org/invidious) or [`Piped`](https://github.com/TeamPiped/Piped). Search either front end instance for a channel and click the RSS symbol. 

The link should look something like this (Invidious): `https://yewtu.be/feed/channel/UCs-QBT4qkj_YiQw1ZntDO3g`.

Or this (Piped): `https://pa.il.ax/feed/unauthenticated/rss?channels=UCs-QBT4qkj_YiQw1ZntDO3g`.

2. Use YouTube's official RSS which comes with some extra info. Follow the first method to find the channel ID (the unique string of random numbers and letters), then slot it into the example link below.

The link should look something like this: `https://www.youtube.com/feeds/videos.xml?channel_id=UCs-QBT4qkj_YiQw1ZntDO3g`.

# Other stuff of interest
* [`ff2mpv`](https://github.com/woodruffw/ff2mpv) - An extension for firefox and chromium-based browsers that lets you right click links and open them in mpv. [Installation guide](https://youtube.com/watch?v=jfyt5ueyWN8).
* [`Invidious`](https://github.com/iv-org/invidious) - Light weight, privacy respecting front end for YouTube.
* [`Piped`](https://github.com/TeamPiped/Piped) - Light weight, privacy respecting front end for YouTube with many extra features.
* [`HyperPipe`](https://codeberg.org/Hyperpipe/Hyperpipe) - Privacy respecting front end for YouTube Music.
* [`NewPipeSponsorBlock`](https://github.com/gilbsgilbs/NewPipeSponsorBlock) - YouTube front end for android with many extra features.
* [`Libredirect`](https://github.com/libredirect/libredirect) - Extension to automatically redirect links to privacy respecting front ends.
