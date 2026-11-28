# CineFuze

**A media center for Android TV boxes with Dolby Vision FEL and 3D Blu-ray (MVC)** – built for the Dune HD and
R_volution players with an Amlogic S928X or S905X4, on its own libVLC build that
plays the full enhancement layer of Dolby Vision profile 7 (composited on the GPU of the S928X, by the Dolby Vision core
of the S905X4) and 3D Blu-ray (MVC) in frame packing. It also runs on Dune HD boxes with Homatics firmware, there
without Dolby Vision FEL, and on Realtek players (Dune HD Real Vision 4K, Zappiti – experimental), where Dolby Vision
plays in the box's own Realtek player under CineFuze's controls.

[![Sponsor](https://img.shields.io/badge/Sponsor-%E2%98%95%20buy%20me%20a%20coffee-ea4aaa?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/djnice)

![Movies wall](screenshots/movies.jpg)

> **CineFuze 1.7.5:** **WebDAV servers** as library sources (a cloud service such as Premiumize, a Nextcloud, a NAS);
> **Emby servers** next to Jellyfin; **music and photo libraries** from Jellyfin and Emby (a "Home videos and photos"
> library split into its photos and its videos); the server's **intro markers** for "Skip intro" and Emby's preview
> images; NFO-only TV shows with their own show, season and episode images; one show in two sources is one show;
> German titles written without umlauts found; old FLAC rips' accented tags read right; white descriptions in Vision
> Discovery Gold.
>
> **1.7.4:** a **theme** to choose: the usual CineFuze blue or **Vision Discovery Gold** (a gold logo, gold
> focus and highlights), under Settings → Appearance → Theme.
>
> **1.7.3:** **local Kodi .nfo files** (their ids, titles and plots) and the posters / fanart beside your
> movies and shows; the music player shows the track's **lyrics** (embedded or a .lrc beside it, synced lines follow
> the music); movies and series from **add-ons on Watch later** and your own lists; on Realtek players smooth 1080p
> movies at the right refresh rate, audio and subtitle delay and subtitle files with the box's own engine too, and a
> smoother poster wall.
>
> **1.7.2:** **an actor's movies** from their biography, the ones in your library first; the biography
> scrolls with Up / Down; [three rows of small posters](#screenshots) in the 2.39:1 band again; CineFuze can **start on
> an add-on** (Cinemeta, say); DVDs on the hardware decoder at the disc's own rate (Amlogic and Realtek); 3D Blu-ray
> fixes on the S905X4 (controls and subtitles in both eyes, no HDMI handshake at a seek); on Realtek players DTS as
> PCM where the TV lacks it and UHD Blu-ray ISOs with the box's own engine; separate PLAY / PAUSE and FAST FORWARD /
> REWIND for **Control4** and other home automation; shows in a TV source's root each their own; a seasons row that
> scrolls.
>
> **1.7.1:** **Java menus (BD-J) of 3D Blu-rays** played by CineFuze itself, the movie taken over in 3D
> (experimental; the Java runtime, 22 MB, is downloaded once under Settings → Playback); a collection's posters with
> your rating and the 4K band; a movie file replaced under the same name read again.
>
> **1.7:** in frame packed **3D the menus, the INFO panel and the subtitles are in both eyes**; 3D discs with their
> menu, taken over in 3D; fast chapter jumps and seeks on 3D discs; preview images for Blu-ray discs; on Realtek
> players 4K movies with the box's own player engine.
>
> **1.6:** 3D Blu-ray ISOs no longer freeze; 3D films made of several clips in 3D; **DVD ISOs** with their menu in
> CineFuze's own player; a 3D tab; the front display shows the movie's time; an episode's files as one card.
>
> **1.5:** your own rating (0.5–10) and your own lists (Favourites, Watch later and lists of your own), each list
> with its own wall, menu tab and home row; a TV show inside a movies-only source.
>
> **1.4.3:** [favorite scene playlists](#screenshots); Jellyfin movies grouped into their collections; own images,
> title and year for a movie TMDB doesn't have; the blue key opens Settings; help for every setting.
>
> **1.4.2:** DTS:X / DTS-HD MA in MP4 passed through as DTS-HD; refresh rate switching on Zidoo and other
> Amlogic boxes without the Dune firmware; Jellyfin collections' movies all listed; bitstream behind an HDMI audio
> extractor; Back ends on the home screen.
>
> **1.4.1:** login to Jellyfin 12 servers fixed; add-on addresses, IPTV playlists and the TMDB server
> **typed on your phone** (a QR code on the TV); **library backup and restore**; hide watched titles with any order; a
> "Not identified" category; Extras folders left off the walls; the AUDIO / SUBTITLE keys open the track list;
> subtitles switched on for audio in another language; subtitle brightness and calmer colours; audio delay in 5 ms
> steps; 16:9-only or 2.39:1-only layouts and the red key to switch them; the poster wall and the letters row wrap.
>
> **1.4:** a [Home screen](#screenshots) (off by default) with a spotlight, rows and tiles, in 16:9 and in the 2.39:1
> band; **Jellyfin servers** as library sources, played without transcoding, watch states synced; library
> statistics; 3D and 4K in the Category filter; better matching of names with "3D", "UHD" or "Disc 1"; public live TV
> channels by country (iptv-org).
>
> **1.3.9:** HDR10+ kept on Philips (MediaTek) TVs; Dolby Vision + HDR10+ hybrids recognised from the video stream;
> HDR10+ as Dolby Vision on Realtek players; DTS and real channel counts in MP4; faster seek previews.
> **1.3.8:** text subtitles drawn by the app itself; subtitles from feliratok.eu; your own posters next to the films.

> **Since 1.3:** [AI subtitle translation](#subtitles) (Gemini) while the movie plays; subtitles from OpenSubtitles,
> from the network or the box's drives, with a subtitle delay; frame stepping on Amlogic boxes, Blu-rays included;
> number keys jump to a tenth of the movie; a search of your own library; HDR10+ movies as Dolby Vision 8.1, frame
> by frame from their own HDR10+ metadata; 4K AV1 on the hardware decoder; spoiler protection for TV shows; the age
> rating and the production countries on the movie page; an editable top menu; a "4K ULTRA HD" band on 4K posters;
> no stutter with interlaced movies.

> **CineFuze 1.3:** 4K and Collections tabs for the top menu (off by default); audio delay; the audio track's bit depth
> and bitrate in the INFO panel; a Simplified Chinese interface; DSF / DFF (DSD) music sounds right; on Zidoo boxes
> (experimental) music plays through the Zidoo's own music engine at the file's own sample rate.

> **New in CineFuze 1.2:** [add-ons](#add-ons) – Stremio add-ons and CineFuze add-on files bring catalogs, search
> and playback from the web ([write your own](ADDONS.md)); add-ons' movies open on the movie page with the trailer,
> extras, cast and IMDb's technical specs; your own categories with sources of their own that stay off the main
> walls; movies and TV shows in one source, told apart by themselves; the Watching tab first; Skiptro intro skipping;
> subtitle color; the interface in German, French, Spanish and Swedish too; sources that refresh by themselves.

> **CineFuze 1.0** is out. It runs on every S928X player of Dune HD and R_volution; it is tested on the Dune HD Pro
> 8K Plus, Pro One 8K Plus and the R_volution PlayerPro 8K (S928X), the Dune HD Pro Vision 4K (S905X4, firmware r24
> and r25; the S905X4 players of R_volution are not tested yet) and the Dune HD Premier 4K Pro with Homatics
> firmware (Android 14, no Dolby Vision FEL). This repository presents the app and hosts its APK under
> [Releases](https://github.com/djnice/cinefuze/releases).

## Features

### Library
- Sources: SMB shares (NAS, PC) and the box's own drives (USB, internal HDD); network browsing (NetBIOS, mDNS) that
  reuses the logins the player's firmware has saved; **WebDAV servers** (a cloud service such as Premiumize, a
  Nextcloud, a NAS) over HTTPS with a login, read only.
- **Jellyfin and Emby servers** as sources: login by password (or Quick Connect on Jellyfin), the movies and TV shows
  with the server's own metadata and artwork, its music and photos too, played as they are (Dolby Vision, HDR10+, no transcoding), resume points and watched marks
  synced with the server.
- **Statistics:** movies, files with every version, 4K, 3D, Dolby Vision, HDR10+, collections, TV shows and episodes.
- **Library backup and restore:** identifications, artwork, watched states and resume points saved in the player's
  storage, and restored from there into another CineFuze on the same player.
- Movies, TV shows, other videos, photos and music, each with its own wall – or all of them as folders.
- **Your own categories** (Kids, Concerts, Documentaries …): a type and the sources it comes from, a tab of its own in
  the top menu if you like. A source can be **categories only**, so its titles stay off the main walls; a new source
  can be added right in a category.
- **Your own lists:** Favourites, Watch later and as many lists of your own as you like ("Snowy landscapes", "Must
  rewatch" …), independent of the folders – a movie or TV show can be in several, an add-on's movies and series
  too. "Lists…" in a poster's menu, the green key for Favourites; a title you watch leaves Watch later. Each list has
  its own wall, a tab in the menu, a home tile with a cover of your own; the posters show how many lists a title is in.
- **Your own rating** from 0.5 to 10 on movies and TV shows: on the poster's corner, a "My rating" sort and a home row.
- **Movies and TV shows in one source:** episodes are told apart by their names (S01E02, 1x02, Season 1 …), their
  folders and TMDB; whatever it gets wrong can be set by hand from the item's menu – in a movies-only source too, and a
  whole season in one file is found as well.
- Sources refresh by themselves when files change on the server (SMB change notifications while the app is open, a
  regular check otherwise); an unplugged drive's titles leave the walls and come back with it.
- Local Kodi .nfo files beside the movies and shows (their TMDB / IMDb ids, titles, sort titles and plots) and the
  poster / fanart images next to them, when you keep your library in Kodi's way.
- Metadata from TMDB in 20 languages (titles, plots, posters, backdrops, title logos), disc art from fanart.tv; the
  age rating of your TMDB language's country and the production countries. A TMDB id, an IMDb id or a link can be
  typed when a title needs to be matched by hand, and the results show their posters and plots; an own TMDB server
  address and key where TMDB cannot be reached.
- **Your own posters:** an image next to the film (`<film>-poster.jpg`, `<film>.jpg`, or poster / folder / cover in a
  one-film folder, as Kodi and Plex name them), or one picked from the film's folder in the poster window.
- **Search your own library:** movies, TV shows, collections and albums while you type (accents and a leading
  article do not matter); titles sort without their leading article ("The Matrix" under M), or as written.
- Media info read from the files themselves: resolution, HDR10 / HDR10+ / HLG, Dolby Vision profile and FEL / MEL,
  MaxCLL / MaxFALL and the RPU's L6, Atmos / DTS:X / DTS-HD MA audio, subtitles, chapters.
- Watched marks, resume points and favorite scenes shared between boxes through the NAS.
- The interface in English, Hungarian, Russian, German, French, Spanish, Swedish and Simplified Chinese (Settings →
  Appearance; the last five machine translated – corrections are welcome).

### Browsing
- **Home screen** (off by default): a spotlight with the movies under way and the new ones, rows (Watching, Recently
  added, New episodes, Collections, Top rated, My top rated, Favourites, Watch later, In the most lists) and two rows
  of tiles for tabs, categories, lists and genres, each with a cover image of your own if you like; in 16:9 and in
  the 2.39:1 band.
- Four wall layouts: Highlight (the selected title large), Full wall, Large posters, and 2.39:1 variants for scope
  screens, three rows of small posters among them.
- Sort and filter chips: A–Z, rating, runtime, date added, year, category (3D and 4K first, then TMDB's genres),
  folder; page jumps with P+ / P-.
- Detail page: backdrop, title logo, director, a technical card, cast with face-centred photos and biographies
  (an actor's movies from there, the ones in your library first), extras (TMDB videos, soundtrack), and the trailer in a soft-edged window.
- TV shows: episode detection from folders and file names, season posters, episode stills, a countdown to the next
  episode, Blu-ray discs whose chapters are episodes.
- **Folders view** for those who prefer folders to a poster wall: the sources' folders and files as a list, the file
  name first and the library's title, year, size, length, badges and watched / resume marks with it, and a preview
  beside it. Movies play with their resume point, an album plays from the song you pick, photos open in the viewer.
- **Edit the top menu:** keep only Movies, or any set of walls and categories, in the order you like (Settings →
  Appearance → Menu).
- **Spoiler protection** for TV shows: the stills of unwatched episodes blurred, their plots hidden if you like.
- A **"4K ULTRA HD" band** across 4K movies' posters, as on a UHD Blu-ray case (off by default).
- Versions of a movie (4K and 1080p, 3D, other cuts) on one poster, in collections too; the Versions window shows
  each one's file name. The labels on the posters (versions, collection size, edition) can be turned off.
- **Watching** comes first: the movies and TV shows you left part way, the latest first – from the library and from
  add-ons.

### Playback
- **Dolby Vision profile 7 FEL with its full enhancement layer**, composited on the GPU on the S928X and by the
  SoC's own Dolby Vision core on the S905X4 (MEL is converted to profile 8.1); dual-track (DTDL) MP4 and Blu-ray m2ts.
- **3D Blu-ray (MVC)** in frame packing for 3D projectors and TVs (MKV / MK3D, m2ts, SSIF).
- **Audio as an HDMI bitstream:** Dolby TrueHD / Atmos, DTS-HD MA / DTS:X, E-AC-3, AC-3, DTS – per format, only what
  the receiver takes.
- **HDR10+ as Dolby Vision:** HDR10+ movies can play as Dolby Vision 8.1 on a Dolby Vision TV, frame by frame from
  their own HDR10+ metadata (a setting, off by default); hybrid Dolby Vision + HDR10+ movies play as either.
- 4K AV1 on the box's hardware decoder.
- Automatic refresh-rate switching, seek previews (made in the background), chapters, audio / subtitle panels, an
  INFO panel (the stream, the live Dolby Vision levels – L1 L2 L5 L6 and CM v4.0's L8–L11 –, the audio, the
  subtitle, the chip's temperature and load), and a FEL demo mode on the S928X (split screen: FEL vs. the base
  layer).
- **Frame stepping** on Amlogic boxes: Left / Right steps one frame while paused, exact on Blu-rays too; the number
  keys jump to a tenth of the movie (5: half way, 0: the start).
- Audio delay, subtitle delay.
- **Blu-ray folders and ISO images:** you choose how a disc starts – with its menu in the player's own Blu-ray player
  (HDMV and BD-J; when the main movie starts, CineFuze takes over, over SMB or NFS), or without the menu, straight into
  the main movie in CineFuze; a movie can resume without the menu too. A 3D Blu-ray's Java (BD-J) menu can be played
  by CineFuze itself on S928X / S905X4 boxes (experimental, the
  [Java runtime](https://github.com/djnice/bdj-runtime-android) downloaded once).
- **DVD ISO images:** in CineFuze's own player on every box, with the disc's menu (the remote's arrows and OK steer
  it; MENU brings it back from the chapter list) or straight into the movie, with the resume point.
- **Audio and subtitle languages:** the languages a movie starts with, a "forced subtitles only" mode as in Kodi, and
  the tracks you switch to are remembered for each movie (each episode).
- The Dune HD player can be chosen instead: movies and episodes then play in the Dune HD's own player (its OSD), with
  the resume point saved; a disc starts with its menu or in lite mode.
- Favorite scenes: mark the start and end while a movie plays; scene cards and a timeline on the detail page;
  playlists of scenes from any movies, played back to back (a Scenes tab).
- **Skiptro:** a TV episode with its `.skiptro.json` beside it shows "OK: skip the intro" while the intro runs.

### Subtitles
- Text subtitles (subtitle files, an MKV's SRT / ASS / WebVTT tracks) are drawn by CineFuze itself over the picture,
  as the Dune HD player draws them; Blu-ray (PGS) subtitles by libVLC. Color, size (up to 200 %), background.
- Subtitle files next to the movie (`Movie.hu.srt`, a `Subs` folder) are found by themselves; any file can be loaded
  from the network (SMB) or the box's drives while the movie plays, in its language's usual code page when it is not
  UTF-8.
- **Online subtitles** from the subtitle panel, with no key or login: OpenSubtitles (for your own files the
  subtitles made for that very release first, by the file's hash; for add-on streams by the IMDb id) and, with a
  Hungarian TMDB language, feliratok.eu.
- **AI subtitle translation (Gemini):** an English text subtitle translated to your language while the movie plays,
  ahead of the playing position; a free Google AI Studio key is enough (typed, or pasted from a phone by a QR code).
  Lines translated once are kept for the next time.

### Add-ons
- **Stremio add-ons** by their manifest address (Cinemeta, for example): their catalogs as walls with search,
  year and genre pickers; stream add-ons give the sources a title plays from.
- **CineFuze add-on files:** a JSON file that maps a website's own API to catalogs, item pages and streams – logins,
  multi-step streams and Widevine DRM included, no code. How to write one: **[ADDONS.md](ADDONS.md)**.
- A movie an add-on knows by its IMDb id opens on the **movie page**: TMDB's artwork and texts in your language, the
  trailer, extras and cast, and **IMDb's technical specs** (sound mix, aspect ratio, camera, negative, process) in the
  technical card.
- Add-ons are added in Settings → Add-ons (an address, an `smb://` path or a file on the box), each can have a tab of
  its own; adult add-ons ask once before they open. Add-on files can also build catalogs from TMDB's lists.

### Live TV (IPTV)
- Your provider's **M3U playlist** (URL or file) or **Xtream Codes** login, with its **XMLTV programme guide**.
- Channel list by group with logos and what is on now; now and next with descriptions; favorites.
- Zapping with Up / Down, P+ / P- or the channel number, a now / next bar, audio and subtitle tracks, automatic
  reconnect, refresh-rate switching.
- **Archive (catch-up):** channels with an archive in the playlist (`tvg-rec`, `catchup`, `catchup-source`) or on
  the Xtream server get an ARCHIVE tag; Right opens the past days' programs, OK plays one from its start, with
  seeking and the next / previous program.
- **Public channels by country:** a country's free-to-air list from [iptv-org](https://github.com/iptv-org/iptv)
  added with a country picker.
- CineFuze contains no channels: it plays the playlist you add, or the iptv-org list you pick.

### Photos and music
- Photo wall with dates and places (EXIF), a full-screen viewer with a map of where the photo was taken.
- A live slideshow rendered on the GPU in five styles – Polaroid, Photo table, Gallery wall, Collage zoom, Memories – with music
  if you like.
- Music: album wall with covers and disc art (MusicBrainz, Cover Art Archive, fanart.tv), an album page with a
  spinning record, and a player with four visualizers – bars, ring, neon wave and piano lights – and the track's
  lyrics (embedded in the file or a .lrc beside it, synced ones following the music); FLAC, APE and the
  usual formats, WavPack and DSD (DSF / DFF). On Dune HD boxes music can play at the track's own sample rate; on
  Zidoo boxes (experimental) through the Zidoo's own music engine.

### On the player's firmware
- Firmware without the Dolby Vision FEL module (Dune HD r22 / r24): the kernel module is installed with the first
  FEL movie (it asks once if root access is off, and switches it back off).
- Firmware with the module (Dune HD r25): the firmware brings it itself; CineFuze uses it and installs nothing.
  Newer modules come as a Dune HD plugin from [vlc-fel-android](https://github.com/djnice/vlc-fel-android/releases).
- R_volution players are handled the same way.
- S905X4 (Dune HD r24; r22 not tested): CineFuze installs its S905X4 kernel module with the first FEL movie, the same
  way. 3D MVC is not tested there yet. R_volution's S905X4 players should work the same way, but are not tested yet.
- Dune HD boxes with Homatics firmware (the Dune HD app on Android 14): **no Dolby Vision FEL** (there is no root
  access for the kernel module); everything else plays. The Dune HD app's player can be chosen, TrueHD is decoded by
  the TV / amplifier or by the box (a setting), Dolby Vision movies get black letterbox bars, and the refresh rate
  follows Android's "match content frame rate" setting.
- Realtek players (Dune HD Real Vision 4K, Zappiti – experimental): Dolby Vision movies play in the box's own Realtek
  player, with Dolby Vision output and CineFuze's controls, tracks, subtitles and resume; profile 7 FEL plays without
  its enhancement layer, as in the Dune HD player. Blu-ray folders and ISO images start with the Dune HD's disc menu
  and the main movie goes on in CineFuze. Frame stepping is not possible there.

## Screenshots

New in 1.7.2:

![Three rows of small posters in 2.39:1](screenshots/1.7.2/small-posters-scope.jpg)
<sub>Three rows of small posters in the 2.39:1 band of a CinemaScope screen</sub>

| | |
|---|---|
| ![Actor biography](screenshots/1.7.2/actor-biography.jpg)<br><sub>An actor's biography: Up / Down scroll it, the "Movies" button under the photo</sub> | ![Actor's movies](screenshots/1.7.2/actor-movies.jpg)<br><sub>The actor's movies, the ones in your library first; OK opens a movie's page</sub> |

New in 1.4.3:

| | |
|---|---|
| ![Scene playlist](screenshots/1.4.3/scene-playlist.jpg)<br><sub>Favorite scene playlists: scenes of several movies, played back to back or repeated</sub> | ![Add to playlist](screenshots/1.4.3/scenes-add-to-playlist.jpg)<br><sub>A movie's favorite scenes: Add to playlist</sub> |

New in 1.4:

| | |
|---|---|
| ![Home screen](screenshots/1.4/home.jpg)<br><sub>Home screen: the spotlight and Watching</sub> | ![Home screen in 2.39:1](screenshots/1.4/home-scope.jpg)<br><sub>The Home screen in the 2.39:1 band of a scope screen</sub> |
| ![Home screen tiles](screenshots/1.4/home-rows.jpg)<br><sub>Tiles for tabs, categories and genres, with their counts</sub> | ![Category filter](screenshots/1.4/category-4k.jpg)<br><sub>The Category filter: 4K (and 3D) first, then TMDB's genres</sub> |
| ![Identify](screenshots/1.4/identify.jpg)<br><sub>Identify: the focused result's poster and plot – the same title, years apart</sub> | ![Statistics](screenshots/1.4/statistics.jpg)<br><sub>Statistics of the library</sub> |

New in 1.2:

| | |
|---|---|
| ![Add-ons](screenshots/1.2/addons-cinemeta.jpg)<br><sub>Add-ons: a Stremio catalog (Cinemeta) with its chips, search and genre picker</sub> | ![Add-on movie page](screenshots/1.2/addon-movie-page.jpg)<br><sub>An add-on's movie on the movie page, with IMDb's technical specs</sub> |
| ![Own category](screenshots/1.2/own-category.jpg)<br><sub>An own category (Kids) in the top menu, Watching first</sub> | ![Categories only](screenshots/1.2/categories-only.jpg)<br><sub>Categories: a source "categories only", a new source right in the category</sub> |

![Playback with seek preview](screenshots/playback.jpg)
<sub>Playback: chapters, Dolby Vision P7 FEL, the seek preview while scrubbing. (The box's screen capture leaves out
the video plane; the movie frame behind the controls is taken from the same file at the same time.)</sub>

| | |
|---|---|
| ![Detail page](screenshots/detail.jpg)<br><sub>Detail page: title logo, the trailer in a soft-edged window, the technical card (Dolby Vision P7 FEL)</sub> | ![Technical card](screenshots/techinfo.jpg)<br><sub>MaxCLL / MaxFALL and the RPU's L6, the audio tracks with Atmos</sub> |
| ![Favorite scenes](screenshots/scenes.jpg)<br><sub>Favorite scenes: cards, the movie's timeline, play / loop</sub> | ![Extras](screenshots/extras.jpg)<br><sub>Extras: trailers and the soundtrack</sub> |
| ![Cast](screenshots/cast.jpg)<br><sub>Cast biography</sub> | ![TV show](screenshots/tvshow.jpg)<br><sub>TV show: seasons, episode stills, watched marks</sub> |
| ![Full wall](screenshots/layout-wall.jpg)<br><sub>Full wall layout</sub> | ![Large posters](screenshots/layout-large.jpg)<br><sub>Large posters layout</sub> |
| ![2.39:1](screenshots/layout-scope.jpg)<br><sub>Highlight layout for 2.39:1 screens</sub> | ![Settings](screenshots/settings.jpg)<br><sub>Settings</sub> |
| ![Photos](screenshots/photos.jpg)<br><sub>Photo wall with dates and places</sub> | ![Slideshow](screenshots/slideshow.jpg)<br><sub>Slideshow, Polaroid style</sub> |
| ![Album](screenshots/album.jpg)<br><sub>Album page with cover and disc art</sub> | ![Music player](screenshots/music.jpg)<br><sub>Music player with the spectrum visualizer</sub> |
| ![Folders view](screenshots/folders.jpg)<br><sub>Folders view: file names, the library's facts, the preview</sub> | ![Menu tabs](screenshots/menu-tabs.jpg)<br><sub>Choosing the top menu's tabs</sub> |
| ![Full-screen wall](screenshots/1.0-RC3/01-fullscreen-wall.jpg)<br><sub>Full-screen wall</sub> | ![INFO panel](screenshots/1.0-RC3/16-info-panel.jpg)<br><sub>INFO panel: the stream, Dolby Vision, the audio</sub> |

![Piano lights visualizer](screenshots/music-piano.jpg)
<sub>The music player's piano lights visualizer: rainbow silk waves and spikes over a keyboard whose keys light up
with their own frequency band.</sub>

![Live TV](screenshots/live-tv.jpg)
<sub>Live TV: groups, channels with what is on now, now and next. (A public playlist from iptv-org with an
epgshare01 guide; the programme texts are the guide's, in Hungarian.)</sub>

## Requirements
- A Dune HD or R_volution player with an Amlogic S928X or S905X4 (a Dune HD with Dune HD or Homatics firmware),
  Android 11 or newer; or a Realtek player (Dune HD Real Vision 4K, Zappiti – experimental).
- A 32-bit (armeabi-v7a) userspace.
- Media on a NAS / PC (SMB), a WebDAV, Jellyfin or Emby server, or a drive connected to the box.

## Download
The APK is under [Releases](https://github.com/djnice/cinefuze/releases). Install it like any other APK on the box
(from a USB stick or a network share); a newer APK installs over the old one and keeps the library.

- **Updates (from 1.0):** at its start CineFuze looks for a newer release here and offers it with its changelog;
  Settings → About shows the version and checks by hand. The first time, Android asks you to allow CineFuze to install
  apps.
- CineFuze is a complete player on its own, with its own FEL playback library: VLC FEL is not needed. VLC FEL is a
  separate app (VLC with FEL); the two can be installed side by side, neither needs the other.
- It does not touch the Dune HD firmware's own player. On r25 the Dune HD plugin (`dune_plugin_s928x_dvfel`) loads
  the FEL kernel module at boot; CineFuze uses that module and installs nothing (no module, no boot script, no root
  question), so FEL in the Dune HD player works as before. The two are updated separately: the plugin from the
  [vlc-fel-android releases](https://github.com/djnice/vlc-fel-android/releases) (for the Dune HD player), CineFuze
  with a newer APK.
- CineFuze appears as one more app among the box's apps: start it from there, and the Home button takes you back to
  the Dune HD shell. It does not replace the launcher and has no start-at-boot option of its own.

## Related projects
- [vlc-fel-android](https://github.com/djnice/vlc-fel-android) – the libVLC patches, the `dvfel` kernel module and
  `libdvfelplay` that CineFuze plays Dolby Vision FEL with, the 3D MVC decoding (GPL), and the Dune HD plugin.

## Credits
This product uses the TMDB API but is not endorsed or certified by TMDB. Disc art from fanart.tv, music metadata
from MusicBrainz and the Cover Art Archive, maps © OpenStreetMap contributors. Video playback by VLC (libVLC).
Movie artwork and channel logos in the screenshots belong to their owners.

Dolby, Dolby Vision, Dolby Atmos and Dolby TrueHD are trademarks of Dolby Laboratories; DTS, DTS-HD and DTS:X are
trademarks of DTS, Inc.; Dune HD and R_volution are trademarks of their owners. CineFuze is not affiliated with any
of them.
