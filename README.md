# ytcli-guide

This repository is intended to provide a consolidated guide for accessing YouTube from the command line on Linux. Much of this guide is also applicable to other operating systems.

---

# Contents

* [`Programs`](#Programs)
* [`Scripts`](#Scripts for mpv)
* [`Configuration`](#Configuration)
* [`Aliases`](#Useful)
* [`Tips`](#Tips)
* [`RSS`](#RSS)
* [`Important`](#Important)
* [`Extra`](#Other)

---

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

Some scripts have their own optional configs. These should be placed in `~/.config/mpv/script-opts`.

Some scripts have 'configs' inside the script itself. 

* [`quality-menu`](https://github.com/christoph-heinrich/mpv-quality-menu) - Allows you to select video quality.
* [`youtube-upnext`](https://github.com/cvzi/mpv-youtube-upnext) - Fetches recommended videos for the current video.
* [`reload`](https://github.com/sibwaf/mpv-scripts/blob/master/reload.lua) - Allows you to reload the current video if it freezes. Restarts where you left off.
* [`mpv_sponsorblock`](https://github.com/po5/mpv_sponsorblock) - Automatically skips sponsored ads and other video segments. Also adds markers for these on the osc timeline.
* [`copy-permalink`](https://gist.github.com/2084x/699fe48cff983bcbaf532d82e1515269) - Copies link of current video. Useful with 'com' alias.
* [`auto-keep-gui-open`](https://github.com/VideoPlayerCode/mpv-tools/blob/master/scripts/auto-keep-gui-open.lua) - When a file ends, keeps the gui open if it's a video and closes if it's audio. Useful if you want to scrape recommended videos after a video ends.

Some I don't use but may be of interest:

* [`mpv-youtube-search`](https://github.com/rozari0/mpv-youtube-search) / [`youtube-search`](https://github.com/CogentRedTester/mpv-scripts/blob/master/youtube-search.lua) - Two scripts to search YouTube from inside mpv.
* [`mpv_sponsorblock_minimal`](https://github.com/bbhtt/mpv_sponsorblock_minimal) - Lightweight mpv_sponsorblock. Some features missing.
* [`mpv-ytdlAutoFormat`](https://github.com/Samillion/mpv-ytdlautoformat) - Automatically change videos to your desired resolution. (I prefer to set this in my yt-dlp config).
* [`youtube-download`](https://github.com/cvzi/mpv-youtube-download) - Download current video with a single key press.
* [`ytdl-preload`](https://gist.github.com/bitingsock/17d90e3deeb35b5f75e55adb19098f58) - Preloads next video in the playlist by downloading to a local tmp file. Can be buggy and download audio and video out of sync.

A full list of scripts for mpv can be found [here](https://github.com/mpv-player/mpv/wiki/User-Scripts). 

# Configuration
## ytfzf
### `~/.config/ytfzf/conf.sh`

This is the main config file.
```sh
pages_to_scrape=1
sub_link_count=5
show_thumbnails=1
url_handler_opts="--force-window=yes"
```

### `~/.config/ytfzf/submenu-conf.sh`

This is the config file for playlist and channel pages.

```sh
show_thumbnails=1
url_handler_opts="--force-window=yes"
enable_back_button=0
```

Increase page and sub counts to scrape more videos by default.

Occasionally a video will play in audio only mode, so I like to force a window.

You can optionally set `FZF_DEFAULT_OPTS` in these configs, but I prefer to set them in my shell profile. The options I use are: 

`FZF_DEFAULT_OPTS="--layout=reverse -e -m --height=100% --bind=ctrl-a:select-all"`

## yt-dlp
### `~/.config/yt-dlp/config`

```sh
-o ~/dl/%(title)s.%(ext)s
--sponsorblock-remove default
-S "height:2160"
```

The options in your yt-dlp config will be respected by mpv when playing network streams. For example, I set my max resolution to be 4K, since anything higher always stutters.

## mpv
### `~/.config/mpv/mpv.conf`

Not going to include my entire config here, just the relevant parts.

```sh
slang=eng,en # auto select eng subs where possible
reset-on-next-file=pause # prevent video starting paused
```

# Useful shell aliases
Make a search for all types of results (channel, playlist and video) and download high quality thumbnails.

`yts="ytfzf --type=all --thumbnail-quality=maxresdefault"` 

Search for a channel and browse it's videos.

`ytc="ytfzf --type=channel"`

Scrape comments of a video. Search term should be video link. Does not download channel avatars because they can sometimes cover up long comments.

`com="ytfzf -c comments --skip-thumb-download"`

Scrape your subscription file and sort by upload date.

`sub="ytfzf -c SI --sort"`

# Tips for ytfzf
* Run `ytfzf --rii` occasionally to refresh healthy invidious instances.
* ctrl-j/k moves you up and down the list of search results.
* alt-p will scrape the next page of search results.
* You can use tab to select and open multiple videos. First selected video will play and others will be added to the playlist in order.
* ctrl-a (with my fzf settings) will allow you to select and open all videos. Useful for playlists.
* You can add a subscriptions file to scrape a list of channels for their most recent uploads. See the ytfzf manual for how the file should be set up.
* If you use dwm + swallow patch and want to loop menus, add `is_detach=1` to your config, otherwise thumbnails and menus will break when the window is restored.

# RSS
Although you can get subscriptions with ytfzf, it's not the most efficient way to do so for a few reasons:

1. You can only scrape a small number of videos per channel before it becomes excessive.
2. Large subscription files will take a long time to load.
3. You have to manually check for new videos.

Instead we can use RSS to get updates for new videos. Feeds can be viewed in an RSS feed reader such as [`newsboat`](https://github.com/newsboat/newsboat) and be updated automatically in the background. I suggest using Luke Smith's [dot files](https://github.com/LukeSmithxyz/voidrice/blob/master/.config/newsboat/config) and [scripts](https://github.com/LukeSmithxyz/voidrice/blob/master/.local/bin/cron/newsup) to quickly set up newsboat and a cronjob to do updates. There are many other readers you could use, but I like newsboat because it's terminal based and can open videos in mpv with a single key press.

There are two ways to get an RSS feed link for YouTube:

1. Use the built in RSS on [`Invidious`](https://github.com/iv-org/invidious) or [`Piped`](https://github.com/TeamPiped/Piped). Search an instance of either front end for a channel and click the RSS symbol. 

The link should look something like this (Invidious): `https://yewtu.be/feed/channel/UCs-QBT4qkj_YiQw1ZntDO3g`.

Or this (Piped): `https://piped.kavin.rocks/feed/unauthenticated/rss?channels=UCs-QBT4qkj_YiQw1ZntDO3g`.

2. Use YouTube's official RSS. Follow the first method to find the channel ID (the unique string of random numbers and letters), then slot it into the example link below.

The link should look something like this: `https://www.youtube.com/feeds/videos.xml?channel_id=UCs-QBT4qkj_YiQw1ZntDO3g`.

Piped feeds currently provide less information than YouTube and Invidious. 

Invidious provides thumbnails, which neither Piped nor YouTube do.

# Important Update
As of Late 2022 YouTube has updated the way videos display on channel pages, making it troublesome to scrape them. Previously when a channel was scraped you would get all videos, but now you will only get 30 (the first page). The current work around is to scrape the channel as a playlist, since every channel has one for all it's videos. To do this you will need to add the following lines to `~/.config/ytfzf/conf.sh` and use the flag `--all-videos` with your aliases. 

```sh
#keeps track of whether or not --all-videos is used
CONFIG_invidious_all_videos=0

#is run when --all-videos is parsed
on_opt_parse_all_videos () {
    CONFIG_invidious_all_videos=1

   #returning 1 says to override the default  behavior (in this case that would be printing an error)
    return 1
}

#is run right before it starts scraping
ext_on_search () {
    search=$1
    scrape=$2

    #only  want to change stuff if the scrape is invidious-channel and --all-videos was used.
    if [ "$scrape" = "invidious-channel" ] && [ "$CONFIG_invidious_all_videos" -eq 1 ]; then
        #overrides the current scraper
        curr_scrape=invidious_playlist

        #gets the channel id for the playlist link
        set_real_channel_url_and_id "$search"

        #overrides the search with a new one
        _search="https://www.youtube.com/playlist?list=$channel_id"
    fi
}
```

# Other stuff of interest
## Alternative front ends

Alternative front ends allow for lightweight, privacy respecting YouTube browsing.

* [`Invidious`](https://github.com/iv-org/invidious) - Alternative YouTube frontend.
* [`Piped`](https://github.com/TeamPiped/Piped) - Alternative YouTube frontend with built in Sponsorblock and Return YouTube Dislike.
* [`HyperPipe`](https://codeberg.org/Hyperpipe/Hyperpipe) - Alternative YouTube Music front end.
* [`NewPipeSponsorBlock`](https://github.com/gilbsgilbs/NewPipeSponsorBlock) - Alternative YouTube frontend for android with built in Sponsorblock and Return YouTube Dislike.

## Extensions

* [`ff2mpv`](https://github.com/woodruffw/ff2mpv) - Lets you right click links and open them in mpv. [Installation guide](https://youtube.com/watch?v=jfyt5ueyWN8).
* [`Libredirect`](https://github.com/libredirect/libredirect) - Automatically redirects links to privacy respecting alternative front ends.
* [`Return YouTube Dislike`](https://github.com/Anarios/return-youtube-dislike) - Brings back dislikes.
* [`Sponsorblock`](https://github.com/ajayyy/SponsorBlock) - Automatically skips sponsored ads and other video segments.
* [`Violentmonkey`](https://github.com/violentmonkey/violentmonkey) - Provides userscript support for browsers.

## Userscripts
Use these with Violentmonkey to make regular YouTube work better.
* [`Simple YouTube Age Restriction Bypass`](https://greasyfork.org/en/scripts/423851-simple-youtube-age-restriction-bypass) - Watch age restricted videos without login or age verification.
* [`Youtube shorts redirect`](https://greasyfork.org/en/scripts/439993-youtube-shorts-redirect) - Redirects any `*.youtube.com/shorts/*` link to `*.youtube.com/watch?v=*`.
* [`Resize YT To Window Size`](https://greasyfork.org/en/scripts/811-resize-yt-to-window-size) - Moves the video to the top of the page and fills the entire window with the player.
* [`Return YouTube Dislike`](https://greasyfork.org/en/scripts/436115-return-youtube-dislike) - Userscript alternative for the Return YouTube Dislike extension.
* [`'Video paused. Continue watching?' Auto confirmer`](https://greasyfork.org/en/scripts/377506-youtube-video-paused-continue-watching-auto-confirmer) - Automatically clicks 'Ok' for the 'Video paused. Continue watching?' dialog.
* [`Kill YouTube Channel Video Autoplay`](https://greasyfork.org/en/scripts/399862-kill-youtube-channel-video-autoplay) - Kill autoplay on channel and user pages.
* [`Youtube Unblocker Improved`](https://greasyfork.org/en/scripts/405713-youtube-unblocker-improved) - Auto redirects blocked videos to `invidious.snopyta.org`.
