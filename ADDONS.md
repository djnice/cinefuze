# CineFuze add-ons: developer reference

This document describes how CineFuze loads, shows and plays add-ons, and gives the complete format of a
**CineFuze add-on file**. It is written for people who want to build an add-on for CineFuze or check why an
existing one behaves the way it does.

Contents

1. [What an add-on is](#1-what-an-add-on-is)
2. [Installing and managing add-ons](#2-installing-and-managing-add-ons)
3. [How add-ons appear in CineFuze](#3-how-add-ons-appear-in-cinefuze)
4. [Stremio add-ons](#4-stremio-add-ons)
5. [CineFuze add-on files](#5-cinefuze-add-on-files)
6. [Templates](#6-templates)
7. [Condition objects](#7-condition-objects)
8. [The `catalog` block](#8-the-catalog-block)
9. [The `catalogs` block (list catalogs)](#9-the-catalogs-block-list-catalogs)
10. [The `meta` block and episodes](#10-the-meta-block-and-episodes)
11. [The `stream` block](#11-the-stream-block)
12. [DRM (Widevine)](#12-drm-widevine)
13. [Logins (`auth`)](#13-logins-auth)
14. [Worked examples](#14-worked-examples)
15. [IMDb ids, TMDB artwork and the movie page](#15-imdb-ids-tmdb-artwork-and-the-movie-page)
16. [Playback of add-on streams](#16-playback-of-add-on-streams)
17. [Limits, and when to write a Stremio add-on instead](#17-limits-and-when-to-write-a-stremio-add-on-instead)
18. [Troubleshooting](#18-troubleshooting)

---

## 1. What an add-on is

An add-on brings movies, series and live streams from outside the user's own library into CineFuze. Its
language is the [Stremio add-on protocol](https://github.com/Stremio/stremio-addon-sdk/tree/master/docs):
a **manifest** that lists **catalogs**, the catalogs' **items** (Stremio "meta previews"), an item's
**details** (a "meta", with a series' episodes as "videos") and an item's **streams**.

CineFuze knows two kinds of add-ons. Both share the same manifest and the same user interface:

| Kind | What it is | Where the data comes from |
|---|---|---|
| **Stremio add-on** | A server that speaks the Stremio protocol: `…/manifest.json`, `…/catalog/…`, `…/meta/…`, `…/stream/…` | The add-on's own server |
| **CineFuze add-on file** | A single JSON file: a Stremio-style manifest plus a `cinefuze` block that maps an ordinary website's JSON API onto catalogs, details and streams | The website's API, called directly by CineFuze; no server of your own |

A file is treated as a CineFuze add-on file when its top-level JSON object has a `cinefuze` key; otherwise
it is a Stremio add-on.

How a catalog is shown depends on its type:

| Catalog | View | OK on an item |
|---|---|---|
| `movie`, `series` (and any other type) | Poster wall: 7 columns of 2:3 posters with the name and a line under it | The item's page, then a list of sources |
| `tv`, `channel`, or any catalog with `"posterShape": "landscape"` | Live wall: 4 columns of 16:9 pictures, refreshed every minute, with viewers and info | Plays at once, full screen; Up / Down switches to the previous / next item |

---

## 2. Installing and managing add-ons

All of this is in **Settings → Add-ons**.

### 2.1 Adding an add-on

Type an address into the **Address** field and press **Add**, or use **Browse the network** (SMB shares)
or **Browse the box's drives** to pick a `.json` file (only folders and `.json` files are listed).

| Address | Meaning |
|---|---|
| `https://host/path/manifest.json` | A Stremio add-on server, or a CineFuze add-on file served over HTTP(S). Up to 6 redirects are followed. |
| `stremio://host/path/manifest.json` | A Stremio add-on; `stremio://` is read as `https://`. `/manifest.json` is added when missing. |
| `smb://[user[:password]@]host/share/path/file.json` | A CineFuze add-on file on an SMB share. Without a login in the URL, the login of a library source on the same host is used, else guest access. |
| `/absolute/path/file.json` or `file:///absolute/path/file.json` | A CineFuze add-on file on the box itself. |

Notes:

- The file may be gzip-compressed; CineFuze detects gzip by its magic bytes.
- The file must be a JSON object with a non-empty `id` and `name`, or installation fails with
  `not an add-on manifest (no id / name)`.
- A Stremio add-on must be installed from its URL: its other resources are requested relative to the
  manifest address. A Stremio manifest installed from a file has no server to talk to.
- When you pick a file with **Browse the network** and type a login there, the login is used to read the
  file but is **not** stored with the add-on's address. A login typed into an `smb://` URL in the Address
  field is stored as part of that address.

### 2.2 Updating, ordering, removing

Each installed add-on is shown with a tag (**Stremio** or **add-on file**, plus **18+** for adult add-ons),
its name and version, its types, its number of catalogs and its description. Its buttons:

| Button | Effect |
|---|---|
| **On / Off** | Disabled add-ons are not shown and are not asked for details or streams. |
| **Own tab: On / Off** | The add-on gets a header tab of its own (titled with its name) and leaves the shared **Add-ons** tab. Only shown for add-ons that have at least one browsable catalog (see 3.2). |
| **Up** | Moves the add-on one place up. The order matters: details for an item are taken from the first enabled add-on that serves them (see 3.5). |
| **Update** | Reads the manifest / file again from the address it was installed from. |
| **Delete** | Removes the add-on and its stored login. |

An add-on is identified by its manifest `id`. Installing a file with an `id` that is already installed
replaces it in place and keeps its On/Off, Own tab, 18+ confirmation and login.

### 2.3 Logging in

A CineFuze add-on file with an `auth` block (section 13) shows its login state under its buttons
("Not logged in", or "Logged in: *user*" followed by the account's labels), a **Log in** button that opens
user name and password fields, and **Log out** when logged in. A failed login shows `Failed: <reason>`.
The user name, password and session are stored encrypted on the box and never written to the log.

---

## 3. How add-ons appear in CineFuze

### 3.1 The Add-ons tab and own tabs

The **Add-ons** tab shows a row of chips with the enabled add-ons that are not on their own tab and have at
least one browsable catalog. Below it is a row of chips for the selected add-on's catalogs. An add-on on its
own tab shows its catalog chips next to its title.

The catalog row contains, in order:

1. **Search**, if any of the add-on's catalogs has a `search` extra (see 3.3);
2. one chip per browsable catalog (two catalogs with the same name get their type added: *Popular · Movies*,
   *Popular · Series*);
3. **Genre: …** or **Year: …**, if the selected catalog has a `genre` extra with options. The label is
   *Year* when every option is a four-digit number. OK opens a list of the options, with **All** at the top
   unless the genre is required.

The catalog row is left out when the add-on has a single catalog and no search. Catalogs whose `available`
check fails (a catalog that needs an account flag, see 13.1) are hidden. The status text at the right shows
*Loading…*, *The catalog could not be loaded*, *The catalog is empty*, or the number of items (the add-on's
`total` when it gives one).

### 3.2 Browsable catalogs

A catalog is **browsable** (gets a chip) unless it has a required extra other than a `genre` with options.
So a catalog that requires `search` is used only by the Search chip, and a Stremio calendar-style catalog
(required `lastVideosIds`, say) is not shown at all. A catalog that requires `genre` and lists options is
browsable and starts with the first option.

An add-on with no browsable catalog at all (a stream-only "source" add-on, or a search-only add-on) has no
chip on the Add-ons tab and no Own tab button. A source add-on still provides streams on every movie and
episode page (3.5).

### 3.3 Search

The Search chip opens a text field. About 0.7 s after typing stops, CineFuze asks the first catalog of each
type that has a `search` extra (so at most one movies' catalog, one series' catalog, …), page 0 only, and
shows the results together, without duplicates. Search results are not paged and are always shown as
posters.

### 3.4 Paging

When the focus comes within 12 items of the end of the wall, the next page is asked for. Only catalogs that
declare a `skip` extra are paged; a catalog without one is a single page. The `skip` value sent is the
position where the next page starts: for a Stremio add-on, the number of items received so far; for a
CineFuze add-on file, the number of items in the answers so far, including items the file filtered out
(see 8.3).

### 3.5 Item pages, sources and playback

**Live items** (live wall) play at once in a full-screen player. The first stream of the item's own add-on
is played. OK / INFO / Left / Right shows the overlay (name, viewers, info, resolution, position, clock,
description); Up / Down, Channel +/-, Page Up/Down and Previous/Next switch item; Back or Stop returns to
the wall at the item played last. When an item has no stream, the overlay shows *No stream for it* (no
reason given) or *Not available now* (the add-on gave a reason; the reason itself is written to the log
only); any other failure, of the add-on or of playback, shows *The channel does not play*.

**Movies and series** open a page:

- A `movie` item whose id is an IMDb id (`tt…`) and that TMDB knows opens CineFuze's library movie page
  (section 15).
- A `movie` or `series` item whose id is a TMDB id, `tmdb:<number>` (a movie's for `movie`, a TV show's for
  `series`), is first turned into its IMDb id when TMDB knows one; from then on it is that `tt…` item: the
  library movie page, Cinemeta's episodes, the stream add-ons' sources and the watched state all go by the
  IMDb id. Without one, it opens the add-on item page with TMDB's details in CineFuze's TMDB language.
- Every other item opens the add-on item page. Its details come from the **first enabled add-on, in the
  user's order, that serves `meta`** for the item's type and id and answers (for `tt…` ids that is usually
  Cinemeta); when none does, the catalog item's own fields are used. The page shows the background, a facts line
  (release info, runtime, up to three genres, `★ rating` unless the rating is empty or `0`), the logo or
  the title, up to two directors, **Play** (or **Continue from …** with **From the start**), the
  description and, for movies, up to six cast members.
- An item is a series when its type is `series` or its details have videos. A series page shows season
  chips (season 0 is shown last, as *Specials*) and a row of episode cards with thumbnail, code (`2×05`),
  title, release date, a check when watched and a progress bar when under way. **Play** plays the next
  episode to watch. A series without videos shows *No videos for this one at the moment.*

**Play** opens the **Sources** panel: CineFuze asks **every enabled add-on that serves `stream`** for the
item's type and id, all at once, and lists their streams by add-on (the add-on's name as a tag, the
stream's name in bold, its description under it). Episodes are asked for with type `series` and the
episode's id. If no add-on gives a stream, the panel shows the last reason an add-on gave
(see `reason` in section 11) or *No source found…*.

The playback position is kept per stream id; an item counts as watched in the last 5 % (or the last 4
minutes of anything longer than 30 minutes); a position under one minute is not kept. Movies and series
played from add-ons appear on the **Watching** tab while under way (the last 200 are kept). Live items are
not kept there.

### 3.6 Adult add-ons

An add-on whose manifest has `"behaviorHints": {"adult": true}` shows an **18+** screen ("Adults only: this
add-on is for viewers over 18") instead of its catalogs, until the user confirms with **I am over 18**. The
confirmation is kept per add-on, also across updates.

---

## 4. Stremio add-ons

CineFuze implements the client side of the Stremio protocol for the resources `catalog`, `meta` and
`stream`.

### 4.1 Manifest

| Key | Use in CineFuze |
|---|---|
| `id`, `name` | Required. `id` identifies the add-on (updates replace by `id`). |
| `version`, `description` | Shown in Settings. |
| `logo` | An absolute `http(s)` address of the add-on's logo, shown in Settings → Add-ons and on the add-on's chip. PNG, JPEG, WebP, GIF, ICO or SVG (an SVG is drawn on a see-through background). An element of an SVG sprite by its id after `#`: `https://host/sprite.svg#logo-id` (a `<symbol>`, `<g>` or `<svg>` with that `id`). The logo is drawn at the chip's height and as wide as its shape, up to four times its height. |
| `types` | Shown in Settings; the default types of every resource. |
| `resources` | Strings (`"stream"`) or objects (`{"name": "stream", "types": [...], "idPrefixes": [...]}`). Only `meta` and `stream` are checked; catalogs are used whether or not `catalog` is listed. `subtitles` and `addon_catalog` are not used. |
| `idPrefixes` | The ids the resources are for. An empty list, or a list of empty strings, means any id. A resource's own `types` / `idPrefixes` take precedence over the manifest's. |
| `catalogs` | See 4.2. |
| `behaviorHints.adult` | The 18+ gate (3.6). Other `behaviorHints` (`configurable`, …) are ignored; install the configured manifest URL instead. |

An add-on **serves** a resource for an item when the resource is listed, the item's type is in its types,
and the item's id starts with one of its id prefixes (or it has none).

### 4.2 Catalogs and extras

| Catalog key | Use |
|---|---|
| `type`, `id` | Together identify the catalog (an id need only be unique within a type). |
| `name` | The chip's text; defaults to `id`. |
| `posterShape` | `landscape` gives the live wall; anything else (`poster`, `square`, none) the poster wall. |
| `extra` | Objects `{"name", "isRequired", "options"}`. Supported names: `skip`, `search`, `genre`. Other extras are ignored, but a required one makes the catalog not browsable. |
| `genres` | Old form of the `genre` options; used when the `genre` extra has no `options`. |
| `extraSupported` | Old form: each name is added as a non-required extra. (`extraRequired` is not read.) |

The catalog request is

```
{base}/catalog/{type}/{id}.json
{base}/catalog/{type}/{id}/search=…&genre=…&skip=….json
```

where `{base}` is the manifest URL without `/manifest.json`. Extras are added only when set, in the order
`search`, `genre`, `skip`; values are URL-encoded (spaces as `%20`). `skip` is sent only when greater than 0.

### 4.3 Catalog items (`metas`)

| Field | Use |
|---|---|
| `id` | Required; items without one are dropped. |
| `type` | Defaults to the catalog's type. |
| `name`, `description` | HTML entities are decoded. |
| `poster` | The picture (2:3 poster, or 16:9 picture on the live wall). |
| `releaseInfo` (or `year`) | Shown under the name. |
| `posterShape` | Read, but the view follows the catalog, not the item. |
| `viewers` *(CineFuze extension)* | A number; shown on the picture and under the name ("12 345 viewers"). |
| `info` *(CineFuze extension)* | An array of short strings shown under the name, joined with dots. |
| `badge` *(CineFuze extension)* | A short mark on the picture's top right corner ("NEW"). |

A Stremio catalog page is complete when it is empty; CineFuze asks for the next page with `skip` set to the
number of items so far, as long as the catalog declares `skip` and the last page was not empty.

### 4.4 Meta

`{base}/meta/{type}/{id}.json` is requested only from add-ons that serve `meta` for the item. Fields used
from `meta`: `id`, `type`, `name`, `poster`, `background`, `logo`, `description`, `releaseInfo` (or `year`),
`runtime`, `genres` (or `genre`), `imdbRating`, `director`, `cast`, `videos`. Other fields (`links`,
`trailers`, `trailerStreams`, …) are ignored.

Each video: `id` (required), `name` (or `title`), `season`, `episode` (or `number`), `released` (the first
10 characters are shown, so an ISO date), `thumbnail`, `overview` (or `description`). Videos are sorted by
season (season 0 last) and episode.

### 4.5 Streams

`{base}/stream/{type}/{id}.json` is requested from every enabled add-on that serves `stream` for the item.

| Stream field | Use |
|---|---|
| `url` | Required. Streams without a `url` (`infoHash`, `ytId`, `externalUrl` only) are skipped. |
| `name` | The source's title in the Sources panel (line breaks become spaces). |
| `description` (or `title`) | Shown under the name (up to four lines). |
| `behaviorHints.proxyHeaders.request` | HTTP headers sent with the stream's requests (see 16 for when they are applied). `proxyHeaders.response` is ignored. |

Other stream fields (`behaviorHints.notWebReady`, `bingeGroup`, `subtitles`, …) are ignored.

A **source add-on**, such as a torrent search server, typically has `stream` only and no browsable
catalog: it gets no chip or tab, and its streams appear in the Sources panel of every movie and episode it
serves. Its streams must be playable HTTP(S) addresses; CineFuze has no torrent engine.

---

## 5. CineFuze add-on files

### 5.1 A minimal complete example

A catalog of movies from a JSON API, with search, details and a stream:

```json
{
  "id": "com.example.archive",
  "version": "1.0.0",
  "name": "Example Archive",
  "description": "Public-domain movies from the Example Archive API",
  "types": ["movie"],
  "resources": ["catalog", "meta", "stream"],
  "idPrefixes": ["exa:"],
  "catalogs": [
    {"type": "movie", "id": "popular", "name": "Popular",
     "extra": [{"name": "skip"}, {"name": "search"}], "params": {"sort": "popular"}},
    {"type": "movie", "id": "new", "name": "Recently added",
     "extra": [{"name": "skip"}], "params": {"sort": "added"}}
  ],
  "cinefuze": {
    "headers": {"X-Client": "cinefuze"},
    "catalog": {
      "url": "https://api.example.com/v1/films?sort={catalog.sort}&offset={skip}&limit=50",
      "searchUrl": "https://api.example.com/v1/search?q={search}",
      "items": "results",
      "total": "count",
      "meta": {
        "id": "exa:{slug}",
        "name": "{title}",
        "poster": "{images.poster}",
        "description": "{summary}",
        "releaseInfo": "{year}"
      }
    },
    "meta": {
      "url": "https://api.example.com/v1/films/{id}",
      "fields": {
        "name": "{title}",
        "description": "{summary}",
        "poster": "{images.poster}",
        "background": "{images.backdrop}",
        "releaseInfo": "{year}",
        "runtime": "{runtime}",
        "genres": "genres.*.name",
        "rating": "{score}"
      }
    },
    "stream": {
      "url": "https://api.example.com/v1/films/{id}/play",
      "stream": "{sources.[kind=hls].url||sources.0.url}"
    }
  }
}
```

With these answers:

```json
{"count": 1250, "results": [
  {"slug": "the-general-1926", "title": "The General", "year": 1926,
   "summary": "A train engineer…", "images": {"poster": "/img/p/general.jpg"}}
]}
```

```json
{"sources": [{"kind": "mp4", "url": "https://cdn.example.com/general.mp4"},
             {"kind": "hls", "url": "https://cdn.example.com/general/master.m3u8"}]}
```

the wall shows *The General* with the poster `/img/p/general.jpg` (relative: set `base`, see 5.3, to make it
absolute), the item's id is `exa:the-general-1926`, and its stream is the HLS address.

### 5.2 Structure

The file is a Stremio manifest (section 4.1 and 4.2 apply unchanged) plus a `cinefuze` object:

| `cinefuze` key | Type | Purpose | Section |
|---|---|---|---|
| `headers` | object of strings | HTTP headers for every API call and for playback | 5.4 |
| `base` | string | Base URL for relative picture and stream addresses | 5.3 |
| `auth` | object | A login: cookie session or token chain | 13 |
| `catalog` | object | How every catalog's items are fetched | 8 |
| `catalogs` | object of objects | Named alternative catalog specs, selected by a catalog's `params.spec` | 9 |
| `meta` | object | How an item's details (and episodes) are fetched | 10 |
| `meta_<type>` | object | The `meta` block for items of one type (`meta_movie`, `meta_series`, …); takes precedence over `meta` | 10 |
| `stream` | object | How an item's stream is found | 11 |
| `stream_<type>` | object | The `stream` block for items of one type (`stream_tv`, `stream_series`, …); takes precedence over `stream` | 11 |
| *any other name* | object | A stream block reached from a step's `when.use` (e.g. `stream_drm`) | 11.4 |

Manifest keys that matter especially for add-on files:

- **`resources` and `types`** must list `meta` and `stream` for the types your items have, and
  **`idPrefixes`** must match your ids, or the item page gets no details from you and the Sources panel
  does not ask you for streams. (Live items are the exception: the live player asks the item's own add-on
  directly.)
- Episodes are asked for as type `series`, so a file with series needs `series` in the `stream` resource's
  types and episode ids that start with one of the prefixes.
- **`idPrefixes`**: in templates, `{id}` is the item's id **without** the first prefix it starts with
  (`exa:the-general-1926` → `the-general-1926`). Use a prefix of your own, such as `exa:`. If you use IMDb
  ids with `"idPrefixes": ["tt"]`, `{id}` loses the `tt`: write `tt{id}` where the API needs it.
- **Catalog `params`** (an object of strings) is a CineFuze extension: the catalog's own values for the
  templates, read as `{catalog.<key>}`. Two keys have a meaning of their own: `spec` (section 9) and
  `flag` (13.1).

### 5.3 Addresses: `base`

Picture addresses (`poster`, `background`, `logo`, episode `thumbnail`) and the final stream address are
made absolute:

| Value | Result |
|---|---|
| `http://…`, `https://…`, empty | As it is |
| relative (`/img/a.jpg`, `img/a.jpg`, `../a.jpg`), `base` set | Resolved against `base` like a link in a web page |
| `//host/path`, no `base` | `https://host/path` |
| anything else, no `base` | As it is |

```json
"cinefuze": {"base": "https://www.example.com/", "catalog": {"…": "…"}}
```

API call addresses (catalog `url`, step URLs, …) are **not** resolved against `base`; they must be absolute.

### 5.4 HTTP requests

Every API call is an HTTP GET (stream steps can POST, see 11.3) with:

- `User-Agent: Mozilla/5.0 (Linux; Android 11) CineFuze` and `Accept: application/json`, which the file's
  `headers` can replace;
- the file's `headers`;
- the login's cookie or token headers, for the login's hosts only (section 13).

Connect timeout 10 s, read timeout 20 s. A GET must answer **HTTP 200**; any other status is an error.
The answer must be a **JSON object** (an answer that is a top-level JSON array cannot be used, except in
login steps), or contain one that `extract` can cut out (8.1). Pictures are fetched without the file's
headers and without the login.

---

## 6. Templates

Nearly every string in the `cinefuze` block is a **template**: literal text with fields in braces that are
filled from the current JSON answer (or item) and from variables.

### 6.1 Field syntax

| Syntax | Result |
|---|---|
| `{a}` | The value of `a`: a variable named `a` if there is one (6.3), else the field `a` of the current item / answer |
| `{a.b.c}` | A dot path into nested objects |
| `{a.0.b}` | A number selects an array element (from 0) |
| `{a.*.b}` | `*` goes through every element of an array; the template takes the first value found |
| `{a.[k=v].b}` | The elements of array `a` whose field `k` equals `v` (as text); see 6.4 |
| `{a?text}` | `text` when `a` is "true" (6.5), else nothing |
| `{a\|\|b\|\|c}` | The first of `a`, `b`, `c` that has a non-empty value |
| `{a~regex}` | The first capture group of `regex` found in the value of `a`; nothing when it does not match |
| `{a\|path}` | In a URL: the value is URL-encoded segment by segment, its `/` kept (6.6) |
| `$sha1(text)$` | The lowercase hex SHA-1 of `text`, after its fields are filled (6.7) |

In the table, `\|` stands for a plain `|` (escaped for the table only): write `{a||b}` and `{a|path}`.

The forms combine in this order: `$name` variables inside the field are replaced first (6.4), then `||`
alternatives are tried, then the `~regex` is applied to the chosen value, then `?text` tests the result:

```
{title||name}                    the title, else the name
{label~(\d+)\. season||x}        not what it looks like: the regex is "(\d+)\. season||x"
{label||name~(\d+)}              the number in the label (or, without a label, in the name)
{label~(\d+)?Numbered}           "Numbered" when the label contains a number
```

Rules and limits of the field syntax:

- A field cannot contain `{`, `}` or `?` (the `?` starts the condition text). This applies to `~regex`
  too: no `{n}` quantifiers (write `\d\d\d\d`), no `(?i)`, no lazy `*?`, no optional `?`. The condition
  text after `?` cannot contain braces.
- The regex needs a capture group: `{a~(\d+)}`, not `{a~\d+}`.
- Inside JSON, backslashes are doubled: `"{label~(\\d+)\\. season}"`.
- Several fields may follow each other: `"{premium?Paid}{rental?Rent}"`.
- A value that is a whole number stored as a decimal (`5.0`) is written as `5`. `true` / `false` are
  written as such. A missing value is empty. Objects and arrays are written as JSON text.
- Any brace pair with no braces inside it is read as a field. Literal text like a JSON body (`{"a":1}`)
  is therefore not safe in a template: it is replaced (here by nothing). A body with fields inside its
  braces (`{"item":"{id}"}`) works, because the outer braces then contain braces.

### 6.2 Paths in plain keys

Several keys are not templates but **paths** written without braces: `items`, `total`, `searchItems`,
`genres`, `each`, `value`, `next`, and the keys of condition objects (section 7). They use the same dot
syntax: `results`, `data.list.*.item`, `blocks.*.items.*`, `assets.[type=dash].id`.

### 6.3 Variables

| Variable | Available in | Value |
|---|---|---|
| `{skip}` | `catalog` | Where the page starts (0, then as returned) |
| `{page}` | `catalog` | `skip / pageDiv + 1` (integer division; `pageDiv` defaults to 1) |
| `{page}` | `pages`, `more.url` | The page number being fetched (9, 10.2) |
| `{search}` | `catalog` | The search text (empty when not searching) |
| `{genre}` | `catalog` | The picked genre / year (empty for *All*) |
| `{catalog.<key>}` | `catalog`, `catalogs.*` | The catalog's `params.<key>` |
| `{catalog.id}` | `catalog`, `catalogs.*` | The catalog's id |
| `{id}` | `meta`, `stream` | The item's (or episode's) id without the id prefix |
| `{type}` | `meta`, `stream` | The item's type (`movie`, `series`, `tv`, …) |
| `{value}` | `catalogs.*.pages`, `videos.head`, `videos.pages` | The list being fetched (9, 10.2) |
| `{minute}` | everywhere but `auth` | Minutes since 1970: changes once a minute (for picture addresses that must change) |
| `{now}` | everywhere but `auth` | Seconds since 1970 |
| `{tmdb.key}` | everywhere but `auth` | CineFuze's own TMDB API key, so a file built on TMDB's lists needs no key of its own (empty in a build without TMDB) |
| `{tmdb.lang}` | everywhere but `auth` | CineFuze's TMDB language (`hu-HU`, `en-US`, …): the one set in the settings, else the interface's |
| `{wv}` | a `stream` block that has `drm` | `hardware` on a box with Widevine L1, else `software` |
| *saved names* | later steps of the same stream, its `stream` and `drm` | What a step's `save` kept (11.2) |
| `{auth.<name>}` | everywhere but `auth`, when logged in | What a token-chain login kept (13.2); inside `auth` the same values are written without `auth.` |

**Variables take precedence over fields of the same name.** In a stream step, `{id}` is always the item's
id, never an `id` field of the answer; in an episode mapping, `{id}` is the series' id, not the episode
card's. Read such fields by a longer path (`{data.id}`), or choose differently named fields. The names
`id`, `type`, `skip`, `page`, `search`, `genre`, `value`, `minute`, `now`, `wv` and your saved names are
affected in the places where those variables exist.

### 6.4 Array filters: `[k=v,…]`

`{list.[k=v].field}` keeps the elements of the array `list` whose path `k` has the text value `v`, and goes
on with `field` in each. Several conditions separated by commas must all hold; condition paths may be
dotted:

```
{assets.[format=dash].url}
{assets.[format=dash,drm.level=hardware].url||assets.[format=dash].url}
```

`$name` inside a field is replaced by the variable `name` before the field is read (variable names are
letters, digits and `_`; `$auth…` and `$catalog…` cannot be used this way). This makes filters depend on
the box:

```
{assets.[format=dash,security=$wv].url||assets.[format=dash].url}
```

A filter can also stand first, when the answer itself is an array (login steps only, 13.2):
`{[kid=false].id}`.

### 6.5 Truth (`?text`, conditions)

A value is *true* unless it is missing, `null`, `false`, the number 0, the empty string, or the string
`"false"` or `"0"`. Objects and arrays (even empty ones) are true.

### 6.6 URL encoding

In templates that are call addresses (catalog `url` / `searchUrl`, `meta.url`, step URLs, `pages`, `head`,
`more.url`, `enrich.url`, login step URLs) every filled value is URL-encoded (UTF-8, spaces as `%20`):
`{catalog.filter}` with the value `*ch:1;` becomes `*ch%3A1%3B`. Literal text is left as written, so write
characters the server must see unencoded into the template itself.

Two exceptions:

- A template that is **one field and nothing else** (`"{url}"`, `"{next_link}"`) is taken as it is: a
  complete address from an answer.
- `{a|path}` encodes each `/`-separated part but keeps the slashes, for ids that are paths
  (`show/episode 1` → `show/episode%201`).

`?text` output is never encoded. Templates that are not call addresses (names, pictures, the final
`stream`, headers, `save`, `post` bodies) are not encoded.

### 6.7 `$sha1(…)$`

After all fields are filled, every `$sha1(text)$` is replaced by the 40-character lowercase hex SHA-1 of
`text`. Use it for signed picture addresses:

```json
"poster": "https://img.example.com/i/{image_id}.jpg?w=360&sig=$sha1(/i/{image_id}.jpg?w=360SECRETSALT)$"
```

The text may not contain `)$`. In call addresses the hashed text contains the URL-encoded values.

---

## 7. Condition objects

Several keys take an object of `path: value` pairs. The paths are plain paths (6.2) into the current item
or answer; values are compared as text, so `true`, `"true"`, `5` and `"5"` all work. A `null` value
matches a field that is missing, `null` or empty: `"skipIf": {"poster_path": null}` leaves out the items
without a poster.

| Key | Where | Matches when |
|---|---|---|
| `skipIf` | `catalog` | **any** pair matches: the item is left out |
| `skipIfNot` | `catalog` | see 13.1 (any pair) |
| `only` | `catalogs.*` | **any** pair matches: the card is kept |
| `require` | stream step, stream block | **all** pairs match, else the stream is refused with `reason` |
| `refuse` | stream step, stream block | **any** pair matches: the stream is refused with `reason` |
| `when.if` | stream step | **any** pair matches: the item continues with another block |
| `ok` | login step, cookie login | **all** pairs match, else the login fails |

Two keys take **regular expressions** as values (full Java regex syntax, searched anywhere in the text):

| Key | Where | Matches when |
|---|---|---|
| `choose` | `videos` | **all** regexes find a match: the list is used |
| `prefer` | `videos` | **all** regexes find a match in the list's `head` answer |

```json
"choose": {"template": "^EpisodeCard$", "title": "^(?!.*(?i:trailer|interview)).*$"}
```

---

## 8. The `catalog` block

One `catalog` block serves every catalog of the manifest; catalogs differ by their `params`. (A catalog
whose `params.spec` names an entry of `catalogs` uses that instead, section 9.)

| Key | Type | Default | Effect |
|---|---|---|---|
| `url` | template | required | The page's address. Variables: `{skip}`, `{page}`, `{search}`, `{genre}`, `{catalog.*}`, … |
| `searchUrl` | template | none | The address when searching. Without it, a search uses `url` with `{search}`. |
| `extract` | regex | none | The answer is not JSON: its first capture group is (8.1) |
| `items` | path | required | The array of items in the answer; with `*` in it, every value found (8.2) |
| `searchItems` | path | `items` | The items' path in search answers (only with `searchUrl`) |
| `total` | path | none | A number in the answer: the catalog's size (shown, and ends the paging) |
| `pageDiv` | integer | `1` | `{page}` is `skip / pageDiv + 1` |
| `skipIf` | condition | none | Items to leave out (7) |
| `skipIfNot` | object | none | Items to leave out without an account flag (13.1) |
| `meta` | object | none | How an item's fields are filled (8.4) |

### 8.1 `extract`: JSON inside a page

When the API answers with a script or a web page that contains the data, `extract` is a regular expression
(`.` also matches line breaks) whose first capture group is parsed as a JSON object:

```json
"catalog": {
  "url": "https://www.example.com/listing?offset={skip}",
  "extract": "var data = (\\{.*?\\});",
  "items": "list.0.items",
  "meta": {"id": "exa:{url}", "name": "{title}", "poster": "{img}"}
}
```

If the regex does not match, the call fails with `no data in the answer`. `extract` is read in `catalog`
only.

### 8.2 `items`

Without `*`, `items` must lead to an array. With `*`, all the values found are collected, which flattens
nested lists:

```
"items": "results"                  {"results": [{…}, {…}]}
"items": "hits.*.item"              {"hits": [{"item": {…}}, {"item": {…}}]}
"items": "blocks.*.items.*"         {"blocks": [{"items": [{…}]}, {"items": [{…}, {…}]}]}
```

### 8.3 Paging

Paging works only for catalogs with a `skip` extra in the manifest. A next page is assumed while the last
answer had items and, when `total` is given, `skip + items in the answer < total`. Searches are one page.

`{skip}` is the number of items in the answers so far, **counted before `skipIf` / `skipIfNot`**, so the
pages do not slide back over each other when items are filtered out. A page whose items were all filtered
out is skipped automatically and the next one is fetched.

For APIs that count pages instead of items, use `{page}` with `pageDiv` set to the page size:

```json
"catalog": {
  "url": "https://api.example.com/v2/browse?page={page}&per_page=24",
  "pageDiv": 24,
  "items": "data",
  "total": "meta.total",
  "meta": {"id": "exa:{id}", "name": "{name}"}
}
```

With `pageDiv` 24 the pages asked are 1, 2, 3, … provided every page holds 24 items. Without `pageDiv`,
`{page}` is `skip + 1`.

`total` must be a JSON number; a number in a string is ignored.

### 8.4 The item mapping (`meta`)

Each key is a template filled from one item of `items`:

| Key | Effect |
|---|---|
| `id` | The item's id. Items with an empty id are dropped. Begin it with your id prefix: `"exa:{slug}"`. |
| `name` | The name under the picture |
| `poster` | The picture (made absolute, 5.3) |
| `description` | Shown on the live overlay and on the item page; HTML entities decoded, trimmed |
| `releaseInfo` | The line under the name (a year, a category, …) |
| `viewers` | A number shown on the picture ("12 345") |
| `info` | An **array** of templates; the non-empty ones (and not `null`) are joined under the name |
| `badge` | A short mark on the picture, typically `{is_new?NEW}` |

The item's type and poster shape are the catalog's.

```json
"meta": {
  "id": "exa:{slug}",
  "name": "{title}",
  "poster": "https://img.example.com/posters/{slug}.jpg",
  "description": "{summary}",
  "releaseInfo": "{year}",
  "info": ["{country}", "{language}"],
  "badge": "{premium?Premium}"
}
```

---

## 9. The `catalogs` block (list catalogs)

Some sites have no paged listing, only a page made of several lists ("rows", "ribbons"). A catalog whose
`params.spec` is the name of an entry in `catalogs` is built from that entry, in one go, without paging:

| Key | Type | Default | Effect |
|---|---|---|---|
| `url` | template or array of templates | required | The page; with an array, the first address that answers (13.3) |
| `each` | path | | The lists on the page: each value found becomes `{value}`. Without `each`, the items are taken from this one answer. |
| `items` | path | required | Without `each`: the items in the `url` answer (`*` allowed, as in 8.2). With `each`: the array of cards in each `pages` answer (no `*`). |
| `pages` | template | | With `each`: a list's page address (`{value}`, `{page}`) |
| `firstPage` | integer | `0` | The first `{page}` |
| `pageStep` | integer | `1` | The step between page numbers |
| `maxPages` | integer | `20` | The most pages fetched per list |
| `only` | condition | none | Keep only the cards that match (any pair, 7) |
| `meta` | object | | The item mapping, as in 8.4 |
| `enrich` | object | none | One more call per item to fill `releaseInfo` (below) |

A list's pages are fetched from `firstPage` in steps of `pageStep` until `maxPages`, an error, an answer
without the `items` array, or an empty array. Up to six lists are fetched at a time. Items with the same id
appear once. Searches are not supported in list catalogs, and a `skip` beyond 0 returns nothing.

`enrich`: `url` (a template or an array of templates, filled from the card) is fetched for each item and
`releaseInfo` (a template) is filled from that answer, replacing the item's `releaseInfo`. When the call
fails, the item stays as it was.

A catalog of one answer's items. In the manifest's `catalogs` array:

```json
"catalogs": [
  {"type": "tv", "id": "live", "name": "Live", "posterShape": "landscape", "params": {"spec": "home_live"}}
]
```

and in the `cinefuze` block (its `catalogs` is an object of named specs):

```json
"cinefuze": {
  "catalogs": {
    "home_live": {
      "url": "https://api.example.com/home",
      "items": "rows.*.items.*",
      "only": {"kind": "live"},
      "meta": {"id": "exa:{id}", "name": "{title}", "poster": "{image}"}
    }
  }
}
```

A catalog of every list on a page:

```json
"cinefuze": {
  "catalogs": {
    "collection": {
      "url": "https://api.example.com/collections/{catalog.collection}",
      "each": "rowIds.*",
      "pages": "https://api.example.com/rows/{value}?page={page}",
      "firstPage": 1,
      "items": "cards",
      "maxPages": 10,
      "only": {"cardType": "FILM"},
      "meta": {"id": "exa:{slug}", "name": "{title}", "poster": "{imageUrl}"},
      "enrich": {"url": ["https://api.example.com/films/{slug}"], "releaseInfo": "{year}"}
    }
  }
}
```

with the manifest catalog
`{"type": "movie", "id": "classics", "name": "Classics", "params": {"spec": "collection", "collection": "classics"}}`.

---

## 10. The `meta` block and episodes

`meta_<type>`, else `meta`, gives an item's details. It is used when the manifest serves `meta` for the
item (5.2) and the item does not open the library movie page (15).

| Key | Type | Effect |
|---|---|---|
| `url` | template or array of templates | The details' address; `{id}`, `{type}`. With an array, the first one that answers (13.3). |
| `fields` | object | The details (10.1) |
| `videos` | object | A series' episodes (10.2) |

### 10.1 `fields`

| Key | Type | Effect |
|---|---|---|
| `name` | template | The title (HTML entities decoded) |
| `description` | template | The synopsis |
| `poster`, `background`, `logo` | template | Pictures (made absolute); the background fills the page, the logo replaces the title |
| `releaseInfo`, `runtime`, `rating` | template | The facts line; `rating` is shown as `★ 7.4` |
| `genres` | **path** | Every value found is a genre: `"genres.*"`, `"genres.*.name"` |

Director and cast are not read from add-on files. A field left out is empty: the item page does not fall
back to the catalog item's fields once the details have arrived.

```json
"meta": {
  "url": "https://api.example.com/v1/titles/{id}",
  "fields": {
    "name": "{title}",
    "description": "{synopsis||tagline}",
    "poster": "{art.poster}",
    "background": "{art.backdrop}",
    "logo": "{art.logo}",
    "releaseInfo": "{first_aired~(\\d\\d\\d\\d)}",
    "runtime": "{runtime_text}",
    "rating": "{rating}",
    "genres": "genres.*"
  }
}
```

### 10.2 `videos`: a series' episodes

Episodes are collected from **lists** (seasons, rows, blocks) and their **pages**:

| Key | Type | Default | Effect |
|---|---|---|---|
| `each` | path | required | The lists in the details answer: each value is a list (its `{value}`) |
| `value` | path | | Read from each element of `each` to make its `{value}`; without it the element itself is the value |
| `choose` | regex conditions | | Only the elements of `each` that match all of them (7) |
| `more` | object | | Further pages of the details answer that hold more lists (below) |
| `head` | template | | A list's own address, fetched to decide with `prefer` (`{value}`) |
| `prefer` | regex conditions | | The lists whose `head` answer matches; used only together with `head` |
| `pages` | template | required | A list's page address (`{value}`, `{page}`, `{id}`) |
| `items` | path | required | The array of episode cards in each page answer (no `*`) |
| `firstPage` | integer | `0` | The first `{page}` |
| `pageStep` | integer | `1` | The step between page numbers |
| `maxPages` | integer | `60` | The most pages fetched per used list |
| `otherPages` | integer | `10` | With `prefer`: the most pages per list when no list is preferred |
| `video` | object | required | The episode mapping (below) |

How the lists are chosen: with `prefer` and `head`, each list's `head` is fetched and only the lists whose
answer matches are used (up to `maxPages` pages each); when none matches, every list is used with up to
`otherPages` pages each. Without `prefer` (or without `head`), every list is used with up to `maxPages`
pages. Pages are read
until `maxPages`, an error, an answer without the `items` array, or an empty array. Up to six lists are
fetched at a time.

`more`: `{"url": "…{page}…", "from": 2, "next": "pagination.next"}` — while the path `next` has a value in
the last answer (the details answer first), `url` is fetched with `{page}` counting up from `from`
(default 2) and its `each` lists are added; at most 8 further pages.

The episode mapping (`video`), templates filled from one card:

| Key | Effect |
|---|---|
| `id` | The episode's stream id; start it with your id prefix. Cards with an empty id, or an id that is only the first prefix, are dropped. Duplicate ids appear once. |
| `title` | The episode title |
| `season` | A number; missing or 0 means season 1 |
| `episode` | A number; when missing, an episode gets its position (from 1) in its season's list, in the order the pages were received |
| `released` | A date; the first 10 characters are shown |
| `thumbnail` | The still (made absolute) |
| `overview` | The episode synopsis |

Remember that `{id}` is the series' id here (6.3): map the episode id from the card's own field
(`"id": "exa:{episode_uid}"`).

Example: seasons listed in the details, each season paged:

```json
"meta_series": {
  "url": "https://api.example.com/v1/shows/{id}",
  "fields": {"name": "{name}", "description": "{overview}", "poster": "{poster}", "background": "{backdrop}"},
  "videos": {
    "each": "seasons.*.id",
    "pages": "https://api.example.com/v1/seasons/{value}/episodes?page={page}",
    "firstPage": 1,
    "items": "episodes",
    "maxPages": 20,
    "video": {
      "id": "exa:{uid}",
      "title": "{title}",
      "season": "{season_number}",
      "episode": "{number}",
      "released": "{air_date}",
      "thumbnail": "{still}",
      "overview": "{synopsis}"
    }
  }
}
```

Example: the episodes are in rows of a page layout; only rows of the episode-card template whose title is not
a trailer row; the row titles carry the season and episode numbers:

```json
"videos": {
  "each": "blocks.*",
  "value": "blockId",
  "choose": {"template": "^EpisodeCard$", "title": "^(?!.*(?i:trailer|extra)).*$"},
  "more": {"url": "https://api.example.com/v1/shows/{id}/layout?part={page}", "from": 2, "next": "pagination.nextPart"},
  "pages": "https://api.example.com/v1/shows/{id}/blocks/{value}?page={page}",
  "firstPage": 1,
  "items": "items",
  "video": {
    "id": "exa:{target.id}",
    "title": "{label||title}",
    "season": "{label~Season (\\d+)}",
    "episode": "{label~Episode (\\d+)}"
  }
}
```

Example: several rows, of which those with "full episodes" in their header are wanted:

```json
"videos": {
  "each": "tabs.*.rowIds.*",
  "head": "https://api.example.com/rows/{value}",
  "prefer": {"title": "(?i)full episodes"},
  "pages": "https://api.example.com/rows/{value}/{page}",
  "items": "cards",
  "maxPages": 60,
  "otherPages": 5,
  "video": {"id": "exa:{slug}", "title": "{title}", "season": "{seriesInfo.season}", "episode": "{seriesInfo.episode}"}
}
```

---

## 11. The `stream` block

`stream_<type>`, else `stream`, finds the stream of an item (or episode, type `series`). The result is one
stream, listed in the Sources panel under the add-on's name.

| Key | Type | Effect |
|---|---|---|
| `url` | template or array | A single call (when there are no `steps`) |
| `steps` | array | Calls one after the other (11.1). An empty array makes no call. A block needs `url` or `steps`. |
| `require` | condition | All pairs must match the last answer, else refused with `reason` |
| `refuse` | condition | Any pair matching the last answer refuses with `reason` |
| `reason` | template | Why there is no stream, for the user (filled from the last answer) |
| `stream` | template | The stream address, filled from the last answer and the variables; made absolute (5.3) |
| `drm` | object | Widevine licence (section 12) |

If the filled `stream` is empty, the item is refused with `reason`. Each step's answer replaces the one
before; templates always see the last answer's fields plus all variables.

A single call:

```json
"stream": {
  "url": "https://api.example.com/cams/{id}",
  "require": {"status": "online"},
  "reason": "{status}",
  "stream": "{hls}"
}
```

A stream address built from variables alone, with no call:

```json
"stream": {"steps": [], "stream": "https://cdn.example.com/live/{id}/index.m3u8"}
```

### 11.1 Steps

Each element of `steps` is one of:

| Form | Meaning |
|---|---|
| `"https://…{x}…"` | A GET of this address |
| `["https://a/…", "https://b/…"]` | The first of these addresses that answers (13.3) |
| `{ … }` | A step object (below) |

| Step key | Type | Effect |
|---|---|---|
| `url` | template or array | The address(es) |
| `post` | template | Makes the step a POST with this body (11.3) |
| `postType` | string | The POST's `Content-Type`; default `application/json` |
| `require` | condition | All pairs must match this answer, else refused with `reason` |
| `refuse` | condition | Any pair matching this answer refuses with `reason` |
| `reason` | template | The refusal's reason, filled from this answer |
| `save` | object of templates | Values kept as variables for the later steps, `stream` and `drm` (11.2) |
| `need` | string | A variable that must be non-empty after `save`, else refused with `needReason` |
| `needReason` | template | The reason when `need` is missing |
| `when` | object | `{"if": {condition}, "use": "<block name>"}`: continue with another block (11.4) |

The order within one step is: call, `require`, `refuse`, `save`, `need`, `when`. After the last step the
block's own `require` / `refuse` are checked, then `stream` is filled.

A typical two-call stream (the first answer gives a player address, the second the stream):

```json
"stream": {
  "steps": [
    "https://api.example.com/v1/play?item={id}",
    "{player_url}"
  ],
  "stream": "{hls}"
}
```

### 11.2 `save`, `need`, `refuse`

```json
"stream": {
  "steps": [
    {"url": "https://api.example.com/v1/videos/{id}",
     "refuse": {"error.code": "too_many_devices"},
     "reason": "Too many devices on this account. Remove one on the website.",
     "save": {"asset": "{assets.[format=hls].id||assets.0.id}", "region": "{availability.region}"},
     "need": "asset",
     "needReason": "This title is not available with your plan."},
    {"url": "https://api.example.com/v1/assets/{asset}/url?region={region}"}
  ],
  "stream": "{url}"
}
```

Saved values are filled when the step runs and are not URL-encoded until they are used in an address.

### 11.3 POST steps

```json
{"url": "{session_url}", "post": "{session_token}", "postType": "text/plain"}
```

The step POSTs the filled `post` body to `url` (a single template, not a list) with the file's headers, the
login's headers and the `Content-Type`. The answer must be HTTP 2xx and a JSON object. Values in the body
are inserted as they are (not URL-encoded, not JSON-escaped), and the body template follows the brace rule
of 6.1:

```json
{"url": "https://api.example.com/v1/playback", "post": "{\"item\":\"{id}\",\"quality\":\"best\"}"}
```

### 11.4 `when`: another block for some items

A step's `when` switches the rest of the item's stream to another block of the `cinefuze` object, starting
from the current answer and keeping all variables so far. The other block runs its own `steps` (use `[]`
for none, not a missing key), checks, `stream` and `drm`.

```json
"stream": {
  "steps": [
    {"url": "https://api.example.com/v1/items/{id}", "when": {"if": {"protected": true}, "use": "stream_protected"}},
    "https://api.example.com/v1/items/{id}/hls"
  ],
  "stream": "{url}"
},
"stream_protected": {
  "steps": ["https://api.example.com/v1/items/{id}/dash"],
  "stream": "{manifest}",
  "reason": "No protected version came back.",
  "drm": {"license": "https://license.example.com/widevine", "headers": {"x-token": "{licence_token}"}}
}
```

---

## 12. DRM (Widevine)

A stream block with `drm` returns a protected stream played by Media3 with the box's Widevine:

| Key | Type | Effect |
|---|---|---|
| `license` | template | The licence server's address |
| `headers` | object of templates | Headers sent with the licence request |
| `field` | string | When the licence server answers with a JSON object, the field holding the licence in base64; empty: the answer is the raw licence |

The licence request is a POST of the Widevine challenge as it is (`Content-Type:
application/octet-stream`, the CineFuze user agent, then `headers`). An answer that starts with `{` and a
non-empty `field`: the field is base64-decoded (`no licence in the answer` when it is empty); otherwise the
answer is used as the licence. A non-2xx answer fails the playback (logged as `licence: HTTP <code>`).

While a block with `drm` runs, `{wv}` / `$wv` is `hardware` on a Widevine L1 box and `software` otherwise,
so the file can pick the asset that box may play:

```json
"stream": {
  "steps": [
    {"url": "https://api.example.com/v1/videos/{id}/assets",
     "save": {"mpd": "{assets.[format=dash,security=$wv].url||assets.[format=dash].url}", "kid": "{assets.[format=dash].keyId}"},
     "need": "mpd", "needReason": "No version for this box."},
    {"url": "https://api.example.com/v1/licence-token?key={kid}", "save": {"tok": "{token}"}}
  ],
  "stream": "{mpd}",
  "drm": {
    "license": "https://license.example.com/widevine",
    "headers": {"x-auth-token": "{tok}"},
    "field": "license"
  }
}
```

Only Widevine is supported (no PlayReady, no ClearKey). The stream's MIME type is taken from its address:
`.mpd` is DASH, `.m3u8` is HLS, anything else is left to Media3.

---

## 13. Logins (`auth`)

An `auth` block gives the add-on a login (section 2.3). There are two forms: a **cookie login** (a single
POST whose answer sets session cookies) and a **token chain** (any number of calls whose answers' values are
kept). The presence of `login` selects the chain.

### 13.1 Cookie login

```json
"auth": {
  "url": "https://www.example.com/api/login",
  "body": {"email": "{user}", "password": "{password}", "remember": true, "device": {"kind": "tv"}},
  "ok": {"success": true},
  "check": "https://www.example.com/api/me",
  "flags": {"premium": "isPremium"},
  "labels": {"premium": "Premium"},
  "logout": "https://www.example.com/api/logout"
}
```

| Key | Type | Effect |
|---|---|---|
| `url` | string | The login address; its host is the only host that gets the cookies |
| `body` | object | The JSON body. The strings `{user}` and `{password}` are replaced by the JSON-escaped user name and password; write them inside quotes. Other values (nested objects, `null`, booleans) are sent as written. |
| `ok` | condition | Top-level fields of the answer that must have these values, else `login refused` |
| `check` | string | Asked once per run (and after login) with the cookies: the account's flags |
| `flags` | object | `{"<flag>": "<boolean field of the check answer>"}`: the flag is set when the top-level field is true |
| `labels` | object | `{"<flag>": "<text>"}`: how the flags are shown next to the user name |
| `logout` | string | Called (GET, with the cookies) on Log out |
| `lost` | array of integers | HTTP codes that mean the session is gone; default `[401]` |

The login POSTs `body` with `Content-Type: application/json` and the file's headers. The answer must be 2xx,
match `ok`, and set at least one cookie (else `no session in the answer`). The `name=value` parts of all
`Set-Cookie` headers are kept and sent as `Cookie` to the login host's calls.

**Account flags** decide what is shown:

- a catalog with `"params": {"flag": "premium"}` is shown only when the flag is set;
- `skipIfNot` in `catalog`: `{"<flag>": {condition}}` leaves out the items matching the condition (any
  pair) while the account does not have that flag:

```json
"catalog": {
  "url": "https://www.example.com/api/list?offset={skip}",
  "items": "items",
  "skipIfNot": {"premium": {"isPremium": "true"}},
  "meta": {"id": "exa:{slug}", "name": "{title}"}
}
```

Flags exist only with the cookie login; with a token chain no flag is ever set.

### 13.2 Token chain

```json
"auth": {
  "login": [
    {"url": "https://id.example.com/login",
     "form": {"username": "{user}", "password": "{password}", "client": "tv"},
     "ok": {"status": 0},
     "error": "{message}",
     "save": {"uid": "{user.id}", "sig": "{signature}"}}
  ],
  "session": [
    {"url": "https://api.example.com/token",
     "headers": {"x-device-id": "{device}", "x-user": "{uid}", "x-signature": "{sig}", "x-profile": "{profile}"},
     "save": {"jwt": "{token}"}},
    {"url": "https://api.example.com/users/{uid}/profiles",
     "headers": {"Authorization": "Bearer {jwt}"},
     "once": "profile",
     "save": {"profile": "{[kids=false].id}"}}
  ],
  "hosts": ["api.example.com", "cdn-api.example.com"],
  "headers": {"Authorization": "Bearer {jwt}"},
  "expires": "jwt",
  "lost": [401, 498]
}
```

| Key | Type | Effect |
|---|---|---|
| `login` | array of steps | Run once with the user name and password |
| `session` | array of steps | Run after `login`, and again whenever the token is old or the session is lost, from the kept values only |
| `hosts` | array of strings | The host names (exact, case-insensitive) that get `headers` |
| `headers` | object of templates | Headers for calls to `hosts`, filled from the kept values |
| `expires` | string | The kept value that is a JWT; the session is renewed a minute before its `exp` |
| `lost` | array of integers | HTTP codes that mean the session is gone; default `[401]` |
| `logout` | string | Optional: called on Log out as a plain GET, without the token headers |

Each step:

| Step key | Type | Effect |
|---|---|---|
| `url` | template | The address (values URL-encoded) |
| `headers` | object of templates | Headers of this call; a header whose value is empty is not sent |
| `form` | object of templates | Makes the step a POST of an `application/x-www-form-urlencoded` form |
| `body` | object | Makes the step a POST of this JSON object, its fields filled with JSON-escaped values (see the note below) |
| *(neither)* | | A GET |
| `ok` | condition | Paths in the answer that must have these values, else the step fails |
| `error` | template | The failure's message, filled from the answer (`login refused` when empty) |
| `save` | object of templates | Values kept, filled from the answer |
| `once` | string | Skip the step when this value is already kept |

Values available in step templates: `{user}` and `{password}` (login steps only), `{device}` (a random id
made once per add-on and kept even after Log out, for sites that count devices), `{model}` (the box's model
name), and everything saved so far. The answer may be a JSON object or array (`{[kids=false].id}`).

A POST step's non-2xx answer is an error, unless the step has `error`: then its `ok` is checked against
the answer (so a step with `error` should also have `ok`). A GET step's non-200 answer is always an error.

When the login succeeds, the values of the chain are kept (encrypted, together with the user name and
password, which are needed to log in again). All of them except the user name and password are available
to the whole file as `{auth.<name>}`: `{auth.uid}`, `{auth.jwt}`, `{auth.device}`, `{auth.model}`.

Renewal: before a call to one of `hosts`, a token that is old (by `expires`) is renewed by running
`session` again. If `session` fails, the whole login runs again with the stored password. Renewal is tried
at most once a minute. If `expires` is not set, the session is treated as old and refreshed (at most once a
minute) whenever a call to one of `hosts` is made; if the kept value is not a JWT with `exp`, it is never
renewed by age, only when a call answers with a `lost` code.

Note on `body` in chain steps: write a flat object of strings with fields
(`{"login": "{user}", "pass": "{password}"}`). Nested objects without fields in them are not preserved;
use `form`, or the cookie login's `body`, for such requests.

### 13.3 Lost sessions and alternative addresses

- Any API call (catalog, meta, list pages, step GETs) that answers with a `lost` code on a host that
  belongs to the login renews the session (13.2) or logs in again (13.1) and, when that succeeds, is
  repeated once. POST steps are not repeated.
- Where an address may be an **array** (`meta.url`, `catalogs.*.url`, `enrich.url`, a step's `url`, a step
  given as an array), the addresses are tried in order and the first that answers with HTTP 200 is used.
  This covers sites where paid items live under a different path. Errors other than HTTP errors (no
  network, a broken answer) stop at once.

```json
"meta": {
  "url": ["https://www.example.com/api/free/{id|path}", "https://www.example.com/api/paid/{id|path}"],
  "fields": {"name": "{title}"}
}
```

---

## 14. Worked examples

### 14.1 Live cameras

```json
{
  "id": "com.example.naturecams",
  "version": "1.0.0",
  "name": "Example Nature Cams",
  "description": "Live wildlife cameras",
  "types": ["tv"],
  "resources": ["catalog", "stream"],
  "idPrefixes": ["cam:"],
  "catalogs": [
    {"type": "tv", "id": "birds", "name": "Birds", "posterShape": "landscape",
     "extra": [{"name": "skip"}], "params": {"topic": "birds"}},
    {"type": "tv", "id": "ocean", "name": "Ocean", "posterShape": "landscape",
     "extra": [{"name": "skip"}], "params": {"topic": "ocean"}}
  ],
  "cinefuze": {
    "catalog": {
      "url": "https://api.example.com/cams?topic={catalog.topic}&offset={skip}&limit=60",
      "items": "cams",
      "total": "total",
      "skipIf": {"offline": true},
      "meta": {
        "id": "cam:{key}",
        "name": "{title}",
        "poster": "https://img.example.com/cams/{key}/latest.jpg",
        "description": "{about}",
        "viewers": "{watching}",
        "info": ["{region}", "{local_time}"],
        "badge": "{is_new?NEW}"
      }
    },
    "stream": {
      "url": "https://api.example.com/cams/{id}",
      "require": {"status": "online"},
      "reason": "{status}",
      "stream": "{hls}"
    }
  }
}
```

On the live wall, CineFuze fetches every picture again each minute by adding a `cf=<minute>` query
parameter, unless the address carries a signature (`hash=`, `sig=`, `signature=` or `token=` in its query),
which is left untouched. Use `{minute}` in the picture template if the server needs its own parameter.

### 14.2 Movies and series with a cookie login

```json
{
  "id": "com.example.library",
  "version": "1.2.0",
  "name": "Example Library",
  "types": ["movie", "series"],
  "resources": ["catalog", "meta", "stream"],
  "idPrefixes": ["exl:"],
  "catalogs": [
    {"type": "series", "id": "shows", "name": "Shows", "extra": [{"name": "skip"}, {"name": "search"}], "params": {"kind": "show", "access": "all"}},
    {"type": "movie", "id": "films", "name": "Movies", "extra": [{"name": "skip"}, {"name": "search"}], "params": {"kind": "film", "access": "all"}},
    {"type": "movie", "id": "members", "name": "Members only", "extra": [{"name": "skip"}], "params": {"kind": "film", "access": "members", "flag": "member"}}
  ],
  "cinefuze": {
    "base": "https://www.example.com/",
    "auth": {
      "url": "https://www.example.com/api/login",
      "body": {"email": "{user}", "password": "{password}"},
      "ok": {"success": true},
      "check": "https://www.example.com/api/me",
      "flags": {"member": "isMember"},
      "labels": {"member": "Member"},
      "logout": "https://www.example.com/api/logout"
    },
    "catalog": {
      "url": "https://www.example.com/api/list?kind={catalog.kind}&access={catalog.access}&offset={skip}",
      "searchUrl": "https://www.example.com/api/search?kind={catalog.kind}&q={search}",
      "items": "items",
      "total": "total",
      "skipIfNot": {"member": {"membersOnly": true}},
      "meta": {"id": "exl:{slug}", "name": "{title}", "poster": "{poster}", "releaseInfo": "{year}",
               "badge": "{membersOnly?Members}"}
    },
    "meta_series": {
      "url": "https://www.example.com/api/shows/{id}",
      "fields": {"name": "{title}", "description": "{synopsis}", "poster": "{poster}", "background": "{backdrop}", "genres": "genres.*"},
      "videos": {
        "each": "seasons.*.id",
        "pages": "https://www.example.com/api/seasons/{value}?page={page}",
        "items": "episodes",
        "video": {"id": "exl:{slug}", "title": "{title}", "season": "{season}", "episode": "{number}",
                  "thumbnail": "{still}", "released": "{published}"}
      }
    },
    "meta": {
      "url": "https://www.example.com/api/films/{id}",
      "fields": {"name": "{title}", "description": "{synopsis}", "poster": "{poster}", "background": "{backdrop}",
                 "releaseInfo": "{year}", "runtime": "{runtime}", "genres": "genres.*"}
    },
    "stream": {
      "steps": [
        {"url": "https://www.example.com/api/videos/{id}", "require": {"playable": true},
         "reason": "Log in with a member account to watch this."},
        "{playback_url}"
      ],
      "stream": "{hls}"
    }
  }
}
```

The *Members only* catalog is shown only to a logged-in member; in the other catalogs, members-only items
are left out for everyone else. The episodes' ids are `exl:…`, so `stream` (there is no `stream_series`)
also finds episode streams with type `series`.

---

## 15. IMDb ids, TMDB artwork and the movie page

Items with IMDb ids (`tt1234567`, episodes `tt1234567:1:2`) and the types `movie` or `series` are linked to
TMDB:

- **On the wall**, when the TMDB language (the setting, else the interface language) is not English, their
  names and posters are replaced by TMDB's in that language (looked up by IMDb id and cached on the box).
  A title TMDB only knows in the movie's original language is left as the add-on gave it.
- **A `movie` item with a `tt` id that TMDB knows opens CineFuze's library movie page**: TMDB's title,
  overview, year, runtime, genres, rating, director and cast in the TMDB language, the backdrop and title
  logo, the trailer and extras, the cast, IMDb's technical specifications on the technical card, and a
  watched toggle. **Play** opens the Sources panel of every stream add-on (3.5), and playback, the resume
  point and the Watching tab work as for other add-on movies. A `tt` movie TMDB does not know opens the
  add-on item page.
- **Series and other items** open the add-on item page. For `tt` items in a non-English TMDB language, the
  details (from the first add-on that serves `meta`, usually Cinemeta) get TMDB's title, overview, genres,
  cast, backdrop, logo and, for movies, director and runtime; a series' episode titles and synopses are
  localized season by season.

So an add-on file whose API knows IMDb ids can map them as the item ids (`"id": "{imdb_id}"` with
`"idPrefixes": ["tt"]`, remembering that `{id}` then drops the `tt`), get CineFuze's movie page for free,
and also receive streams from any other installed stream add-on for the same movies.

---

## 16. Playback of add-on streams

| Stream | Player | Headers applied |
|---|---|---|
| Item of a live catalog | Media3 (ExoPlayer), full screen | Yes: the stream's headers (`User-Agent` among them) |
| Stream with `drm` | Media3 behind CineFuze's player screen (its OSD, seeking, tracks, resume) | Yes |
| HTTP(S) address that is not `.m3u8` | libVLC through a local relay (`http://127.0.0.1:<port>/…`) | Yes, by the relay |
| `.m3u8` (HLS) address | libVLC directly, starting at the best variant (`:adaptive-logic=highest`) | **No** |
| Any other scheme | libVLC directly | No |

The stream's headers are a Stremio stream's `proxyHeaders.request`, or a CineFuze file's `headers` (not the
login's cookies or tokens).

The relay talks HTTP/1.1 to the server, sends the headers, asks for `Accept-Encoding: identity`, follows
redirects, and when the connection is lost or silent for 45 s reopens it with a `Range` request from the
byte it reached, for as long as the player reads. Servers should therefore support byte ranges. An address
is opened up to three times before the relay gives up with HTTP 502.

HLS playlists are not relayed, because their relative segment addresses would come back to the relay. A
stream that needs headers should therefore not be HLS played on a movie or series page; on a live catalog
the headers do apply. For the same reason, a DASH manifest (`.mpd`) without `drm` on a movie or series page
goes through the relay as a single file, and relative segment addresses in it do not work; give such
streams as progressive files or HLS, or play them on a live catalog. In the live player, a 25 or 30 fps
stream switches the TV to 50 or 60 Hz.

---

## 17. Limits, and when to write a Stremio add-on instead

An add-on file describes requests; it does not run code. It cannot:

- compute anything beyond templates, regex captures and SHA-1: no HMAC, MD5, base64, timestamps in other
  formats, arithmetic (except `{page}`), or parameters a website computes in JavaScript;
- read XML or HTML structure (only JSON, or JSON cut out of a page by `extract` in `catalog`), or answers
  that are a top-level JSON array (outside login steps);
- send per-call headers in catalog, meta or stream steps (only the file-wide `headers` and the login's),
  keep cookies set by answers other than the cookie login's, or POST outside stream steps and logins;
- solve captchas, follow OAuth or device-code sign-ins (showing a code and polling), or use web sockets;
- play PlayReady or ClearKey content (Widevine only);
- give more than one stream per item, subtitles, or director and cast in its details;
- page list catalogs or search in them.

For such sites, write a **small Stremio add-on server** instead and install its manifest URL. It can run
anywhere on the network, for example on a NAS or a home server, in any language. It needs only a handful of
JSON routes:

```
GET /manifest.json
GET /catalog/{type}/{id}.json
GET /catalog/{type}/{id}/{extra}.json        extra: search=…&genre=…&skip=…
GET /meta/{type}/{id}.json
GET /stream/{type}/{id}.json
```

```json
{
  "id": "com.example.homeserver",
  "version": "1.0.0",
  "name": "Home Server",
  "types": ["movie", "series"],
  "resources": ["catalog", "meta", "stream"],
  "idPrefixes": ["hs:"],
  "catalogs": [
    {"type": "movie", "id": "recent", "name": "Recent", "extra": [{"name": "skip"}, {"name": "search"}]}
  ]
}
```

```json
{"metas": [{"id": "hs:42", "type": "movie", "name": "A Film", "poster": "https://…/42.jpg", "releaseInfo": "1954"}]}
```

```json
{"streams": [{"url": "https://media.example.com/42.mkv", "name": "1080p", "description": "Home Server · 8.1 GB",
              "behaviorHints": {"proxyHeaders": {"request": {"Authorization": "Bearer …"}}}}]}
```

The server can do whatever the site needs (sign requests, keep a browser session, convert formats) and give
CineFuze plain answers. The CineFuze extensions `viewers`, `info` and `badge` work in a server's catalog
items too (4.3).

---

## 18. Troubleshooting

CineFuze logs add-on activity to Android's log. On a box with ADB enabled:

```
adb logcat -s CfAddons CfAddonDrm CfRelay CfExoFace
```

| Tag | What it logs |
|---|---|
| `CfAddons` | Installs (`installed <id> <version> from <address> (CineFuze file\|Stremio)`), catalog failures (`<add-on id>/<catalog id>: <error>`), detail and stream failures (`meta …`, `streams <add-on id> <item id>: <error>`), TMDB lookups, playback starts (`play …`) and the live player's errors |
| `CfAddonDrm` | Licence requests: `licence: HTTP <code>, <n> bytes` |
| `CfRelay` | The local relay: `open at <byte>: HTTP <code>…`, `upstream lost at …`, `open failed: …` |
| `CfExoFace` | Media3 playback of DRM streams |

Logins, passwords, tokens, stream addresses of DRM streams and licences are not logged.

Common errors:

| Message | Likely cause |
|---|---|
| `not an add-on manifest (no id / name)` | The file is not a manifest, or lacks `id` / `name` |
| `HTTP 404`, `HTTP 403`, … | A call did not answer 200: check the template's result with the same headers (`curl -H …`) |
| `org.json.JSONException: …` | The answer is not a JSON object (an HTML page, an array): use `extract`, or a server |
| `no data in the answer` | `extract` did not match |
| `not available: <reason>` | `require`, `refuse`, `need` or an empty `stream` refused the item |
| `no session in the answer` | The cookie login's answer set no cookie |
| `login refused` / the site's `error` text | `ok` did not match |
| `no licence in the answer` | The licence answer had no `field` value |
| The catalog is shown but never pages | The catalog has no `skip` extra, or `total` is reached |
| Play shows *No source found* | The manifest does not serve `stream` for the type (`series` for episodes) or the id prefix |
| A field comes out empty or wrong | The path is wrong, or a variable of the same name hides the field (6.3) |
| Pictures do not load | Relative address without `base`, or the picture server wants headers (pictures get none) |
| An HLS stream plays on the live wall but not on a movie page | It needs headers, which HLS on movie and series pages does not get (16) |

When a template does not give what you expect, fetch the call's address yourself, save the answer and walk
the path by hand: every dot is one step into an object or array, `*` takes every element, `[k=v]` keeps the
matching ones, and a template gives the first value found.
