# ytcli-guide

This repository is intended to provide a consolidated guide for accessing YouTube from the command line on Linux.

Much of this guide is also applicable to other operating systems.

# Programs

| Name | Description |
| :--  | :-- |
| [`ytfzf`](https://github.com/pystardust/ytfzf) | A POSIX script to find and watch YouTube videos from the terminal. |
| [`fzf`](https://github.com/junegunn/fzf) | Command-line fuzzy finder. |
| [`ueberzug`](https://github.com/seebye/ueberzug) | X11 terminal image generator. |
| [`mpv`](https://github.com/mpv-player/mpv) | A free, open source, and cross-platform media player. |
| [`yt-dlp`](https://github.com/yt-dlp/yt-dlp) | A youtube-dl fork with additional features and fixes. |
| [`newsboat`](https://github.com/newsboat/newsboat) | An RSS / Atom feed reader for text terminals. |

Wayland thumbnail viewers and alternative menus can be found [here](https://github.com/pystardust/ytfzf#Dependencies).

# Scripts for mpv

User scripts extend the functionality of mpv.

They are placed in `~/.config/mpv/scripts`.

Script configs are placed in `~/.config/mpv/script-opts`. 

**Core:**

| Name | Description |
| :--  | :-- |
| [`quality-menu`](https://github.com/christoph-heinrich/mpv-quality-menu) | Allows you to change video / audio quality and reload files. |
| [`youtube-upnext`](https://github.com/cvzi/mpv-youtube-upnext) | Fetches recommended videos and lets you add them to the playlist. |
| [`playlistmanager`](https://github.com/jonniek/mpv-playlistmanager) | Lets you intuitively manage videos in the playlist. |
| [`sponsorblock_minimal`](https://codeberg.org/jouni/mpv_sponsorblock_minimal) | Automatically skips sponsored segments of videos. |
| [`thumbfast`](https://github.com/po5/thumbfast) | High-performance on-the-fly thumbnailer. |
| [`yap`](https://github.com/2084x/mpv-tools/blob/main/yap.lua) | Yanks, appends and puts paths / links. |
| [`dl`](https://github.com/2084x/mpv-tools/blob/main/dl.lua) | Downloads current video with yt-dlp. |

**Extra:**

| Name | Description |
| :--  | :-- |
| [`youtube-search`](https://github.com/CogentRedTester/mpv-scripts/blob/master/youtube-search.lua) | Search and open YouTube results from within mpv. |
| [`sponsorblock`](https://github.com/po5/mpv_sponsorblock) | Automatically skips sponsored segments of videos. Provides full functionality. |
| [`ytdlAutoFormat`](https://github.com/Samillion/mpv-ytdlautoformat) | Automatically changes videos to your desired resolution based on the domain. |
| [`youtube-download`](https://github.com/cvzi/mpv-youtube-download) | Downloads current audio, video or subtitle with a single key press. |
| [`ytdl-preload`](https://gist.github.com/bitingsock/17d90e3deeb35b5f75e55adb19098f58) | Preloads next entry in the playlist by downloading to a local tmp file. 

A comprehensive list of scripts for mpv can be found [here](https://github.com/mpv-player/mpv/wiki/User-Scripts). 

# Configuration

These are my config files. I recommend reading the manuals to see what all the options do and changing them to suit your use case.

## ytfzf

### `~/.config/ytfzf/conf.sh`

This is the main config file. You can optionally specify configurations for submenus in `~/.config/ytfzf/submenu-conf.sh`, but I prefer to use the same options for both menu types by setting `keep_vars=1`.

```sh
# scraping
search_result_type=all
invidious_instance="https://inv.riverside.rocks"

# menu
show_thumbnails=1
thumbnail_quality=maxres
url_handler_opts="--force-window=yes"
enable_back_button=0
keep_vars=1

# keybinds
download_shortcut=D
video_shortcut=ctrl-o
audio_shortcut=ctrl-x
print_link_shortcut=ctrl-l
info_shortcut=ctrl-i
next_page_shortcut=ctrl-n
detach_shortcut=ctrl-w
search_again_shortcut=ctrl-s
```

## yt-dlp

### `~/.config/yt-dlp/config`

These options will be respected by mpv when playing network streams or when using scripts that call yt-dlp. 

```sh
-o ~/dl/%(title)s.%(ext)s
-S "res:2160"
--sponsorblock-mark all,-poi_highlight
--embed-metadata
--embed-subs
--sub-langs "en.*"
--merge-output-format "mkv"
```

## mpv

### `~/.config/mpv/mpv.conf`

I **highly** recommend reading the [mpv manual](https://mpv.io/manual/master/) to understand what the first five options do. They are dependent on your hardware and are **not** guaranteed to produce good results or even work at all. You may want to use different options or omit them entirely. If you have high-end hardware, it's worth checking out some of the scaling and interpolation options found [here](https://wiki.archlinux.org/title/Mpv#High_quality_configurations) or in the [manual](https://mpv.io/manual/master/#gpu-renderer-options).

```sh
# video
vo=gpu-next
gpu-api=vulkan
profile=gpu-hq
demuxer-max-bytes=1GiB
demuxer-max-back-bytes=1GiB
reset-on-next-file=pause

# audio
alang=jpn,jp,ja,ko,kor,chi,ch,zh,eng,en

# subs
slang=eng,en,en-us
```

My full `mpv.conf` can be found [here](https://github.com/2084x/rice/blob/master/.config/mpv/mpv.conf). 

## shell

### `~/.bash_profile`, `~/.zprofile` etc.

You can optionally set `FZF_DEFAULT_OPTS` in `~/.config/ytfzf/conf.sh`, but I prefer to set them in my shell profile since it's system wide. 

```sh
export FZF_DEFAULT_OPTS="--layout=reverse -e -m --no-separator --ellipsis=~ --bind=ctrl-o:accept,ctrl-space:toggle+down,ctrl-u:page-up,ctrl-d:page-down,ctrl-a:select-all,ctrl-v:deselect-all,ctrl-r:clear-screen,ctrl-i:,ctrl-l:,ctrl-w:"
```

### `~/.bashrc`, `~/.config/shell/aliasrc` etc.

Aliases for commonly used commands.

```sh
yts="ytfzf" # Make a search.
ytl="yts -L | tr -d '\n' | xclip -i -selection clipboard" # Make a search and copy link to the clipboard.
com="yts -c comments --skip-thumb-download" # Scrape comments. Search term should be video link.
```

# RSS

Although you can get subscriptions with ytfzf, it's not the most efficient way to do so for a few reasons:

1. You can only scrape a small number of videos per channel before it becomes excessive.
2. Large subscription files will take a long time to load.
3. You have to manually check for new videos.

Instead we can use RSS to get updates for new videos. Feeds can be viewed in an RSS feed reader such as [`newsboat`](https://github.com/newsboat/newsboat) and be updated automatically in the background. I suggest using Luke Smith's [dot files](https://github.com/LukeSmithxyz/voidrice/blob/master/.config/newsboat/config) and [scripts](https://github.com/LukeSmithxyz/voidrice/blob/master/.local/bin/cron/newsup) to quickly set up newsboat and a cronjob to do updates. There are many other readers you could use, but I like newsboat because it's terminal based and can open videos in mpv with a single key press.

To get a channel's RSS feed:

1. Open a channel page using any [`Invidious`](https://github.com/iv-org/invidious) or [`Piped`](https://github.com/TeamPiped/Piped) instance. The channel ID is the long string of letters and numbers at the end of the link. 
2. Slot the channel ID into either of the following feeds:

* `https://www.youtube.com/feeds/videos.xml?channel_id=$CHANNELID` (for YouTube).
* `https://$INSTANCE/feed/channel/$CHANNELID` (for Invidious).
* `https://$INSTANCE/feed/unauthenticated/rss?channels=$CHANNELID` (for Piped).

Each feed works slightly differently. For example, Piped feeds currently provide less information and Invidious feeds provide thumbnails.

# Important update

As of Late 2022 YouTube has updated the way videos display on channel pages, making it troublesome to scrape them. Previously when a channel was scraped you would get all it's videos, but now you will only get the first thirty results. The current work around is to scrape the channel as a playlist. To do this you will need to add the following script to `~/.config/ytfzf/extensions/allvideos`.

```sh
#!/bin/sh

# addon for getting all videos from a channel after youtube redesign
# to use, add --all-videos to search options
# or set the variable below to 1
all_videos=0

# is run when --all-videos is parsed
on_opt_parse_all_videos () {
    all_videos=1

    # returning 1 says to override the default behavior (in this case that would be printing an error)
    return 1
}

# is run right before it starts scraping
ext_on_search () {
    search=$1
    scrape=$2

    # only want to change stuff if the scrape is invidious-channel and --all-videos was used.
    if [ "$scrape" = "invidious-channel" ] && [ "$all_videos" -eq 1 ]; then
        # overrides the current scraper
        curr_scrape=invidious_playlist

        # gets the channel id for the playlist link
        set_real_channel_url_and_id "$search"

        # overrides the search with a new one
        _search="$invidious_instance/playlist?list=$channel_id"
    fi
}
```

To use the script, place the following line in `~/.config/ytfzf/submenu-conf.sh`.

```sh
load_extension allvideos
```

# Other stuff of interest

## Alternative front ends

Alternative front ends allow for lightweight, privacy respecting YouTube browsing.

| Name | Description |
| :--  | :-- |
| [`Invidious`](https://github.com/iv-org/invidious) | Alternative YouTube frontend. |
| [`Piped`](https://github.com/TeamPiped/Piped) | Alternative YouTube frontend with built in Sponsorblock and Return YouTube Dislike. |
| [`Hyperpipe`](https://codeberg.org/Hyperpipe/Hyperpipe) | Alternative YouTube Music front end. |
| [`NewPipeSponsorBlock`](https://github.com/gilbsgilbs/NewPipeSponsorBlock) | Alternative YouTube frontend for android with built in Sponsorblock and Return YouTube Dislike. |

More frontends for YouTube and similar projects for other sites can be found [here](https://github.com/mendel5/alternative-front-ends).

## Browser extensions

| Name | Description |
| :--  | :-- |
| [`ff2mpv`](https://github.com/woodruffw/ff2mpv) | Lets you right click links and open them in mpv. |
| [`Libredirect`](https://github.com/libredirect/libredirect) | Automatically redirects links to privacy respecting alternative front ends. |
| [`Return YouTube Dislike`](https://github.com/Anarios/return-youtube-dislike) | Brings back dislikes. |
| [`Sponsorblock`](https://github.com/ajayyy/SponsorBlock) | Automatically skips sponsored ads and other video segments. |
| [`Violentmonkey`](https://github.com/violentmonkey/violentmonkey) | Provides userscript support for browsers. |

## Userscripts

Use these with the Violentmonkey extension to make regular YouTube better.

| Name | Description |
| :--  | :-- |
| [`Simple YouTube Age Restriction Bypass`](https://greasyfork.org/en/scripts/423851-simple-youtube-age-restriction-bypass) | Watch age restricted videos without login or age verification.
| [`Youtube shorts redirect`](https://greasyfork.org/en/scripts/439993-youtube-shorts-redirect) | Redirects any `*.youtube.com/shorts/*` link to `*.youtube.com/watch?v=*`.
| [`Resize YT To Window Size`](https://greasyfork.org/en/scripts/811-resize-yt-to-window-size) | Moves the video to the top of the page and fills the entire window with the player.
| [`Return YouTube Dislike`](https://greasyfork.org/en/scripts/436115-return-youtube-dislike) | Userscript alternative for the Return YouTube Dislike extension.
| [`'Continue watching?' Auto confirmer`](https://greasyfork.org/en/scripts/377506-youtube-video-paused-continue-watching-auto-confirmer) | Automatically clicks 'Ok' for the 'Video paused. Continue watching?' dialog.
| [`Kill YouTube Channel Video Autoplay`](https://greasyfork.org/en/scripts/399862-kill-youtube-channel-video-autoplay) | Kill autoplay on channel and user pages.
| [`Youtube Unblocker Improved`](https://greasyfork.org/en/scripts/405713-youtube-unblocker-improved) | Auto redirects blocked videos to `invidious.snopyta.org`.

Many more userscripts can be found [here](https://greasyfork.org/en/scripts/by-site/youtube.com).
