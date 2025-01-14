<div align="center">
<em><img src="https://i.imgur.com/5B48E3N.png" height="90px"></em>
<h1>ArchiveBox<br/><sub>The open-source self-hosted web archive.</sub></h1>

▶️ <a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Quickstart">Quickstart</a> |
<a href="https://archivebox.zervice.io/">Demo</a> |
<a href="https://github.com/ArchiveBox/ArchiveBox">Github</a> |
<a href="https://github.com/ArchiveBox/ArchiveBox/wiki">Documentation</a> |
<a href="#background--motivation">Info & Motivation</a> |
<a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community">Community</a> |
<a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap">Roadmap</a>

<pre>
"Your own personal internet archive" (网站存档 / 爬虫)
</pre>

<!--<a href="http://webchat.freenode.net?channels=ArchiveBox&uio=d4"><img src="https://img.shields.io/badge/Community_chat-IRC-%2328A745.svg"/></a>-->

<a href="https://github.com/ArchiveBox/ArchiveBox/blob/master/LICENSE"><img src="https://img.shields.io/badge/Open_source-MIT-green.svg?logo=git&logoColor=green"/></a>
<a href="https://github.com/ArchiveBox/ArchiveBox/commits/dev"><img src="https://img.shields.io/github/last-commit/ArchiveBox/ArchiveBox.svg?logo=Sublime+Text&logoColor=green&label=Active"/></a>
<a href="https://github.com/ArchiveBox/ArchiveBox"><img src="https://img.shields.io/github/stars/ArchiveBox/ArchiveBox.svg?logo=github&label=Stars&logoColor=blue"/></a>
<a href="https://test.pypi.org/project/archivebox/"><img src="https://img.shields.io/badge/Python-%3E%3D3.7-yellow.svg?logo=python&logoColor=yellow"/></a>
<a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Install#dependencies"><img src="https://img.shields.io/badge/Chromium-%3E%3D59-orange.svg?logo=Google+Chrome&logoColor=orange"/></a>
<a href="https://hub.docker.com/r/archivebox/archivebox"><img src="https://img.shields.io/badge/Docker-all%20platforms-lightblue.svg?logo=docker&logoColor=lightblue"/></a>

<hr/>
</div>

ArchiveBox is a powerful self-hosted internet archiving solution written in Python 3. You feed it URLs of pages you want to archive, and it saves them to disk in a variety of formats depending on the configuration and the content it detects.

Running `archivebox init` in a folder creates a collection with a self-contained `index.sqlite3` index, `ArchiveBox.conf` config file, and folders for each snapshot under `./archive/<timestamp>/`, with human-readable `index.html` and `index.json` files within.

For each URL added with `archivebox add`, ArchiveBox saves several types of HTML snapshot (wget, Chrome headless, singlefile), a PDF, a screenshot, a WARC archive, any git repositories, images, audio, video, subtitles, article text, [and more...](#output-formats)
You can use `archivebox schedule` to ingest URLs regularly from your browser boorkmarks/history, a service like Pocket/Pinboard, RSS feeds, or [and more...](#input-formats)

Archived content is browseable and managable locally with the CLI commands like `archivebox status` or `archivebox list ...`, via the built-in web UI `archivebox server`, directly through the filesystem `./archive/<timestamp>` folders, or via the [Python API](https://docs.archivebox.io/en/latest/modules.html) (alpha) or [REST API](https://github.com/ArchiveBox/ArchiveBox/issues/496) (alpha).

### Quickstart

It works on Linux/BSD (Intel and ARM CPUs with `docker`/`apt`/`pip3`), macOS (with `docker`/`brew`/`pip3`), and Windows (beta with `docker`/`pip3`).

```bash
pip3 install archivebox
archivebox --version
# install extras as-needed, or use one of full setup methods below to get everything out-of-the-box

mkdir ~/archivebox && cd ~/archivebox    # this can be anywhere
archivebox init

archivebox add 'https://example.com'
archivebox schedule --every=day --depth=1 'https://getpocket.com/users/USERNAME/feed/all'
archivebox oneshot --extract=title,favicon,media 'https://www.youtube.com/watch?v=dQw4w9WgXcQ'
archivebox help   # to see more options
```

*(click to expand the sections below for full setup instructions)*

<details>
<summary><b>Get ArchiveBox with <code>docker-compose</code> on any platform (recommended, everything included out-of-the-box)</b></summary>

<i>First make sure you have Docker installed: https://docs.docker.com/get-docker/</i>

<pre lang="bash"><code>
# create a new empty directory and initalize your collection (can be anywhere)
mkdir ~/archivebox && cd ~/archivebox
curl -O 'https://raw.githubusercontent.com/ArchiveBox/ArchiveBox/master/docker-compose.yml'
docker-compose run archivebox init
docker-compose run archivebox --version

# start the webserver and open the UI (optional)
docker-compose run archivebox manage createsuperuser
docker-compose up -d
open 'http://127.0.0.1:8000'

# you can also add links and manage your archive via the CLI:
docker-compose run archivebox add 'https://example.com'
docker-compose run archivebox status
docker-compose run archivebox help  # to see more options
</code></pre>

This is the recommended way to run ArchiveBox because it includes <i>all</i> the extractors like:<br/>
chrome, wget, youtube-dl, git, etc., full-text search w/ sonic, and many other great features.

</details>

<details>
<summary><b>Get ArchiveBox with <code>docker</code> on any platform</b></summary>

<i>First make sure you have Docker installed: https://docs.docker.com/get-docker/</i>

<pre lang="bash"><code>
# create a new empty directory and initalize your collection (can be anywhere)
mkdir ~/archivebox && cd ~/archivebox
docker run -v $PWD:/data -it archivebox/archivebox init
docker run -v $PWD:/data -it archivebox/archivebox --version

# start the webserver and open the UI (optional)
docker run -v $PWD:/data -it archivebox/archivebox manage createsuperuser
docker run -v $PWD:/data -p 8000:8000 archivebox/archivebox server 0.0.0.0:8000
open http://127.0.0.1:8000

# you can also add links and manage your archive via the CLI:
docker run -v $PWD:/data -it archivebox/archivebox add 'https://example.com'
docker run -v $PWD:/data -it archivebox/archivebox status
docker run -v $PWD:/data -it archivebox/archivebox help  # to see more options
</code></pre>

</details>

<details>
<summary><b>Get ArchiveBox with <code>apt</code> on Ubuntu >=20.04</b></summary>

<i>First make sure you're on Ubuntu >= 20.04, or scroll down for older/non-Ubuntu instructions.</i>

<pre lang="bash"><code>
# add the repo to your sources and install the archivebox package using apt
sudo apt install software-properties-common
sudo add-apt-repository -u ppa:archivebox/archivebox
sudo apt install archivebox

# create a new empty directory and initalize your collection (can be anywhere)
mkdir ~/archivebox && cd ~/archivebox
npm install --prefix . 'git+https://github.com/ArchiveBox/ArchiveBox.git'
archivebox init
archivebox --version

# start the webserver and open the web UI (optional)
archivebox manage createsuperuser
archivebox server 0.0.0.0:8000
open http://127.0.0.1:8000

# you can also add URLs and manage the archive via the CLI and filesystem:
archivebox add 'https://example.com'
archivebox status
archivebox list --html --with-headers > index.html
archivebox list --json --with-headers > index.json
archivebox help  # to see more options
</code></pre>

For other Debian-based systems or older Ubuntu systems you can add these sources to `/etc/apt/sources.list`:

<pre lang="bash"><code>
deb http://ppa.launchpad.net/archivebox/archivebox/ubuntu focal main
deb-src http://ppa.launchpad.net/archivebox/archivebox/ubuntu focal main
</code></pre>

Then run `apt update; apt install archivebox; archivebox --version`.

(you may need to install some other dependencies manually however)

</details>

<details>
<summary><b>Get ArchiveBox with <code>brew</code> on macOS >=10.13</b></summary>

<i>First make sure you have Homebrew installed: https://brew.sh/#install</i>

<pre lang="bash"><code>
# install the archivebox package using homebrew
brew install archivebox/archivebox/archivebox

# create a new empty directory and initalize your collection (can be anywhere)
mkdir ~/archivebox && cd ~/archivebox
npm install --prefix . 'git+https://github.com/ArchiveBox/ArchiveBox.git'
archivebox init
archivebox --version

# start the webserver and open the web UI (optional)
archivebox manage createsuperuser
archivebox server 0.0.0.0:8000
open http://127.0.0.1:8000

# you can also add URLs and manage the archive via the CLI and filesystem:
archivebox add 'https://example.com'
archivebox status
archivebox list --html --with-headers > index.html
archivebox list --json --with-headers > index.json
archivebox help  # to see more options
</code></pre>

</details>

<details>
<summary><b>Get ArchiveBox with <code>pip</code> on any platform</b></summary>

<i>First make sure you have Python >= 3.7 installed: https://realpython.com/installing-python/</i>

<pre lang="bash"><code>
# install the archivebox package using pip3
pip3 install archivebox

# create a new empty directory and initalize your collection (can be anywhere)
mkdir ~/archivebox && cd ~/archivebox
npm install --prefix . 'git+https://github.com/ArchiveBox/ArchiveBox.git'
archivebox init
archivebox --version
# Install any missing extras like wget/git/chrome/etc. manually as needed

# start the webserver and open the web UI (optional)
archivebox manage createsuperuser
archivebox server 0.0.0.0:8000
open http://127.0.0.1:8000

# you can also add URLs and manage the archive via the CLI and filesystem:
archivebox add 'https://example.com'
archivebox status
archivebox list --html --with-headers > index.html
archivebox list --json --with-headers > index.json
archivebox help  # to see more options
</code></pre>

</details>
 
---
 
<div align="center">
<img src="https://i.imgur.com/lUuicew.png" width="400px">
<br/>

<a href="https://archivebox.zervice.io">DEMO: archivebox.zervice.io/</a>  
For more information, see the <a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Quickstart">full Quickstart guide</a>, <a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Usage">Usage</a>, and <a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration">Configuration</a> docs.
</div>

---


# Overview

ArchiveBox is a command line tool, self-hostable web-archiving server, and Python library all-in-one. It can be installed on Docker, macOS, and Linux/BSD, and Windows. You can download and install it as a Debian/Ubuntu package, Homebrew package, Python3 package, or a Docker image. No matter which install method you choose, they all provide the same CLI, Web UI, and on-disk data format.

To use ArchiveBox you start by creating a folder for your data to live in (it can be anywhere on your system), and running `archivebox init` inside of it. That will create a sqlite3 index and an `ArchiveBox.conf` file. After that, you can continue to add/export/manage/etc using the CLI `archivebox help`, or you can run the Web UI (recommended). If you only want to archive a single site, you can run `archivebox oneshot` to avoid having to create a whole collection.

The [CLI](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage#CLI-Usage) is considered "stable", the ArchiveBox [Python API](https://docs.archivebox.io/en/latest/modules.html) and [REST API](https://github.com/ArchiveBox/ArchiveBox/issues/496) are "alpha", and the [desktop app](https://github.com/ArchiveBox/desktop) is "alpha".

At the end of the day, the goal is to sleep soundly knowing that the part of the internet you care about will be automatically preserved in multiple, durable long-term formats that will be accessible for decades (or longer). You can also self-host your archivebox server on a public domain to provide archive.org-style public access to your site snapshots.

<div align="center">
<img src="https://i.imgur.com/3tBL7PU.png" width="22%" alt="CLI Screenshot" align="top">
<img src="https://i.imgur.com/viklZNG.png" width="22%" alt="Desktop index screenshot" align="top">
<img src="https://i.imgur.com/RefWsXB.jpg" width="22%" alt="Desktop details page Screenshot"/>
<img src="https://i.imgur.com/M6HhzVx.png" width="22%" alt="Desktop details page Screenshot"/><br/>
<sup><a href="https://archive.sweeting.me/">Demo</a> | <a href="https://github.com/ArchiveBox/ArchiveBox/wiki/Usage">Usage</a></sup>
<br/>
<sub>. . . . . . . . . . . . . . . . . . . . . . . . . . . .</sub>
</div><br/>


## Key Features

- [**Free & open source**](https://github.com/ArchiveBox/ArchiveBox/blob/master/LICENSE), doesn't require signing up for anything, stores all data locally
- [**Powerful, intuitive command line interface**](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage#CLI-Usage) with [modular optional dependencies](#dependencies) 
- [**Comprehensive documentation**](https://github.com/ArchiveBox/ArchiveBox/wiki), [active development](https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap), and [rich community](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community)
- [**Extracts a wide variety of content out-of-the-box**](https://github.com/ArchiveBox/ArchiveBox/issues/51): media w/ youtube-dl, articles w/ readability, code w/ git, [and more...](#output-formats)
- [**Supports scheduled/realtime importing**](https://github.com/ArchiveBox/ArchiveBox/wiki/Scheduled-Archiving) from [many types of sources](#input-formats)
- [**Uses standard, durable, long-term formats**](#saves-lots-of-useful-stuff-for-each-imported-link) like HTML, JSON, PDF, PNG, and WARC
- [**Usable as a oneshot CLI**](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage#CLI-Usage), [**self-hosted web UI**](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage#UI-Usage), [Python API](https://docs.archivebox.io/en/latest/modules.html) (BETA), [REST API](https://github.com/ArchiveBox/ArchiveBox/issues/496) (ALPHA), or [desktop app](https://github.com/ArchiveBox/electron-archivebox) (ALPHA)
- [**Saves all pages to archive.org as well**](https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration#submit_archive_dot_org) by default for redundancy (can be [disabled](https://github.com/ArchiveBox/ArchiveBox/wiki/Security-Overview#stealth-mode) for local-only mode)
- Planned: support for archiving [content requiring a login/paywall/cookies](https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration#chrome_user_data_dir) (working, but ill-advised until some pending fixes are released)
- Planned: support for running [JS scripts during archiving](https://github.com/ArchiveBox/ArchiveBox/issues/51), e.g. to block ads, [scroll pages](https://github.com/ArchiveBox/ArchiveBox/issues/80), [close modals](https://github.com/ArchiveBox/ArchiveBox/issues/175), [expand threads](https://github.com/ArchiveBox/ArchiveBox/issues/345), etc.

## Input formats

ArchiveBox supports many input formats for URLs, including Pocket & Pinboard exports, Browser bookmarks, Browser history, plain text, HTML, markdown, and more!

```bash
echo 'http://example.com' | archivebox add
archivebox add 'https://example.com/some/page'
archivebox add < ~/Downloads/firefox_bookmarks_export.html
archivebox add < any_text_with_urls_in_it.txt
archivebox add --depth=1 'https://example.com/some/downloads.html'
archivebox add --depth=1 'https://news.ycombinator.com#2020-12-12'
```

- <img src="https://nicksweeting.com/images/bookmarks.png" height="22px"/> Browser history or bookmarks exports (Chrome, Firefox, Safari, IE, Opera, and more)
- <img src="https://nicksweeting.com/images/rss.svg" height="22px"/> RSS, XML, JSON, CSV, SQL, HTML, Markdown, TXT, or any other text-based format
- <img src="https://getpocket.com/favicon.ico" height="22px"/> Pocket, Pinboard, Instapaper, Shaarli, Delicious, Reddit Saved Posts, Wallabag, Unmark.it, OneTab, and more

See the [Usage: CLI](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage#CLI-Usage) page for documentation and examples.

It also includes a built-in scheduled import feature with `archivebox schedule` and browser bookmarklet, so you can pull in URLs from RSS feeds, websites, or the filesystem regularly/on-demand.

## Output formats

All of ArchiveBox's state (including the index, snapshot data, and config file) is stored in a single folder called the "ArchiveBox data folder". All `archivebox` CLI commands must be run from inside this folder, and you first create it by running `archivebox init`.

The on-disk layout is optimized to be easy to browse by hand and durable long-term. The main index is a standard sqlite3 database (it can also be exported as static JSON/HTML), and the archive snapshots are organized by date-added timestamp in the `archive/` subfolder. Each snapshot subfolder includes a static JSON and HTML index describing its contents, and the snapshot extrator outputs are plain files within the folder (e.g. `media/example.mp4`, `git/somerepo.git`, `static/someimage.png`, etc.)

```bash
 ls ./archive/<timestamp>/
```

- **Index:** `index.html` & `index.json` HTML and JSON index files containing metadata and details
- **Title:** `title` title of the site
- **Favicon:** `favicon.ico` favicon of the site
- **Headers:** `headers.json` Any HTTP headers the site returns are saved in a json file
- **SingleFile:** `singlefile.html` HTML snapshot rendered with headless Chrome using SingleFile
- **WGET Clone:** `example.com/page-name.html` wget clone of the site, with .html appended if not present
- **WARC:** `warc/<timestamp>.gz` gzipped WARC of all the resources fetched while archiving
- **PDF:** `output.pdf` Printed PDF of site using headless chrome
- **Screenshot:** `screenshot.png` 1440x900 screenshot of site using headless chrome
- **DOM Dump:** `output.html` DOM Dump of the HTML after rendering using headless chrome
- **Readability:** `article.html/json` Article text extraction using Readability
- **URL to Archive.org:** `archive.org.txt` A link to the saved site on archive.org
- **Audio & Video:** `media/` all audio/video files + playlists, including subtitles & metadata with youtube-dl
- **Source Code:** `git/` clone of any repository found on github, bitbucket, or gitlab links
- _More coming soon! See the [Roadmap](https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap)..._

It does everything out-of-the-box by default, but you can disable or tweak [individual archive methods](https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration) via environment variables or config file.

## Dependencies

You don't need to install all the dependencies, ArchiveBox will automatically enable the relevant modules based on whatever you have available, but it's recommended to use the official [Docker image](https://github.com/ArchiveBox/ArchiveBox/wiki/Docker) with everything preinstalled.

If you so choose, you can also install ArchiveBox and its dependencies directly on any Linux or macOS systems using the [automated setup script](https://github.com/ArchiveBox/ArchiveBox/wiki/Quickstart) or the [system package manager](https://github.com/ArchiveBox/ArchiveBox/wiki/Install).

ArchiveBox is written in Python 3 so it requires `python3` and `pip3` available on your system. It also uses a set of optional, but highly recommended external dependencies for archiving sites: `wget` (for plain HTML, static files, and WARC saving), `chromium` (for screenshots, PDFs, JS execution, and more), `youtube-dl` (for audio and video), `git` (for cloning git repos), and `nodejs` (for readability and singlefile), and more.

## Caveats

If you're importing URLs containing secret slugs or pages with private content (e.g Google Docs, CodiMD notepads, etc), you may want to disable some of the extractor modules to avoid leaking private URLs to 3rd party APIs during the archiving process.

```bash
# don't do this:
archivebox add 'https://docs.google.com/document/d/12345somelongsecrethere'
archivebox add 'https://example.com/any/url/you/want/to/keep/secret/'

# without first disabling share the URL with 3rd party APIs:
archivebox config --set SAVE_ARCHIVE_DOT_ORG=False   # disable saving all URLs in Archive.org
archivebox config --set SAVE_FAVICON=False      # optional: only the domain is leaked, not full URL
archivebox config --set CHROME_BINARY=chromium  # optional: switch to chromium to avoid Chrome phoning home to Google
```

Be aware that malicious archived JS can also read the contents of other pages in your archive due to snapshot CSRF and XSS protections being imperfect. See the [Security Overview](https://github.com/ArchiveBox/ArchiveBox/wiki/Security-Overview#stealth-mode) page for more details.

```bash
# visiting an archived page with malicious JS:
https://127.0.0.1:8000/archive/1602401954/example.com/index.html

# example.com/index.js can now make a request to read everything:
https://127.0.0.1:8000/index.html
https://127.0.0.1:8000/archive/*
# then example.com/index.js can send it off to some evil server
```

Support for saving multiple snapshots of each site over time will be [added soon](https://github.com/ArchiveBox/ArchiveBox/issues/179) (along with the ability to view diffs of the changes between runs). For now ArchiveBox is designed to only archive each URL with each extractor type once. A workaround to take multiple snapshots of the same URL is to make them slightly different by adding a hash:

```bash
archivebox add 'https://example.com#2020-10-24'
...
archivebox add 'https://example.com#2020-10-25'
```

---

<div align="center">
<img src="https://i.imgur.com/PVO88AZ.png" width="80%"/>
</div>

---

# Background & Motivation

Vast treasure troves of knowledge are lost every day on the internet to link rot. As a society, we have an imperative to preserve some important parts of that treasure, just like we preserve our books, paintings, and music in physical libraries long after the originals go out of print or fade into obscurity.

Whether it's to resist censorship by saving articles before they get taken down or edited, or
just to save a collection of early 2010's flash games you love to play, having the tools to
archive internet content enables to you save the stuff you care most about before it disappears.

<div align="center">
<img src="https://i.imgur.com/bC6eZcV.png" width="50%"/><br/>
 <sup><i>Image from <a href="https://digiday.com/media/wtf-link-rot/">WTF is Link Rot?</a>...</i><br/></sup>
</div>

The balance between the permanence and ephemeral nature of content on the internet is part of what makes it beautiful.
I don't think everything should be preserved in an automated fashion, making all content permanent and never removable, but I do think people should be able to decide for themselves and effectively archive specific content that they care about.

Because modern websites are complicated and often rely on dynamic content,
ArchiveBox archives the sites in **several different formats** beyond what public archiving services like Archive.org and Archive.is are capable of saving. Using multiple methods and the market-dominant browser to execute JS ensures we can save even the most complex, finicky websites in at least a few high-quality, long-term data formats.

All the archived links are stored by date bookmarked in `./archive/<timestamp>`, and everything is indexed nicely with JSON & HTML files. The intent is for all the content to be viewable with common software in 50 - 100 years without needing to run ArchiveBox in a VM.

## Comparison to Other Projects

▶ **Check out our [community page](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community) for an index of web archiving initiatives and projects.**

<img src="https://i.imgur.com/4nkFjdv.png" width="10%" align="left" alt="comparison"/> The aim of ArchiveBox is to go beyond what the Wayback Machine and other public archiving services can do, by adding a headless browser to replay sessions accurately, and by automatically extracting all the content in multiple redundant formats that will survive being passed down to historians and archivists through many generations.

#### User Interface & Intended Purpose

ArchiveBox differentiates itself from [similar projects](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community#Web-Archiving-Projects) by being a simple, one-shot CLI interface for users to ingest bulk feeds of URLs over extended periods, as opposed to being a backend service that ingests individual, manually-submitted URLs from a web UI. However, we also have the option to add urls via a web interface through our Django frontend.

#### Private Local Archives vs Centralized Public Archives

Unlike crawler software that starts from a seed URL and works outwards, or public tools like Archive.org designed for users to manually submit links from the public internet, ArchiveBox tries to be a set-and-forget archiver suitable for archiving your entire browsing history, RSS feeds, or bookmarks, ~~including private/authenticated content that you wouldn't otherwise share with a centralized service~~ (do not do this until v0.5 is released with some security fixes). Also by having each user store their own content locally, we can save much larger portions of everyone's browsing history than a shared centralized service would be able to handle.

#### Storage Requirements

Because ArchiveBox is designed to ingest a firehose of browser history and bookmark feeds to a local disk, it can be much more disk-space intensive than a centralized service like the Internet Archive or Archive.today. However, as storage space gets cheaper and compression improves, you should be able to use it continuously over the years without having to delete anything. In my experience, ArchiveBox uses about 5gb per 1000 articles, but your milage may vary depending on which options you have enabled and what types of sites you're archiving. By default, it archives everything in as many formats as possible, meaning it takes more space than a using a single method, but more content is accurately replayable over extended periods of time. Storage requirements can be reduced by using a compressed/deduplicated filesystem like ZFS/BTRFS, or by setting `SAVE_MEDIA=False` to skip audio & video files.

## Learn more

Whether you want to learn which organizations are the big players in the web archiving space, want to find a specific open-source tool for your web archiving need, or just want to see where archivists hang out online, our Community Wiki page serves as an index of the broader web archiving community. Check it out to learn about some of the coolest web archiving projects and communities on the web!

<img src="https://i.imgur.com/0ZOmOvN.png" width="14%" align="right"/>

- [Community Wiki](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community)
  - [The Master Lists](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community#The-Master-Lists)  
    _Community-maintained indexes of archiving tools and institutions._
  - [Web Archiving Software](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community#Web-Archiving-Projects)  
    _Open source tools and projects in the internet archiving space._
  - [Reading List](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community#Reading-List)  
    _Articles, posts, and blogs relevant to ArchiveBox and web archiving in general._
  - [Communities](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community#Communities)  
    _A collection of the most active internet archiving communities and initiatives._
- Check out the ArchiveBox [Roadmap](https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap) and [Changelog](https://github.com/ArchiveBox/ArchiveBox/wiki/Changelog)
- Learn why archiving the internet is important by reading the "[On the Importance of Web Archiving](https://parameters.ssrc.org/2018/09/on-the-importance-of-web-archiving/)" blog post.
- Or reach out to me for questions and comments via [@ArchiveBoxApp](https://twitter.com/ArchiveBoxApp) or [@theSquashSH](https://twitter.com/thesquashSH) on Twitter.

---

# Documentation

<img src="https://read-the-docs-guidelines.readthedocs-hosted.com/_images/logo-dark.png" width="13%" align="right"/>

We use the [Github wiki system](https://github.com/ArchiveBox/ArchiveBox/wiki) and [Read the Docs](https://archivebox.readthedocs.io/en/latest/) (WIP) for documentation.

You can also access the docs locally by looking in the [`ArchiveBox/docs/`](https://github.com/ArchiveBox/ArchiveBox/wiki/Home) folder.

## Getting Started

- [Quickstart](https://github.com/ArchiveBox/ArchiveBox/wiki/Quickstart)
- [Install](https://github.com/ArchiveBox/ArchiveBox/wiki/Install)
- [Docker](https://github.com/ArchiveBox/ArchiveBox/wiki/Docker)

## Reference

- [Usage](https://github.com/ArchiveBox/ArchiveBox/wiki/Usage)
- [Configuration](https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration)
- [Supported Sources](https://github.com/ArchiveBox/ArchiveBox/wiki/Quickstart#2-get-your-list-of-urls-to-archive)
- [Supported Outputs](https://github.com/ArchiveBox/ArchiveBox/wiki#can-save-these-things-for-each-site)
- [Scheduled Archiving](https://github.com/ArchiveBox/ArchiveBox/wiki/Scheduled-Archiving)
- [Publishing Your Archive](https://github.com/ArchiveBox/ArchiveBox/wiki/Publishing-Your-Archive)
- [Chromium Install](https://github.com/ArchiveBox/ArchiveBox/wiki/Chromium-Install)
- [Security Overview](https://github.com/ArchiveBox/ArchiveBox/wiki/Security-Overview)
- [Troubleshooting](https://github.com/ArchiveBox/ArchiveBox/wiki/Troubleshooting)
- [Python API](https://docs.archivebox.io/en/latest/modules.html) (alpha)
- [REST API](https://github.com/ArchiveBox/ArchiveBox/issues/496) (alpha)

## More Info

- [Tickets](https://github.com/ArchiveBox/ArchiveBox/issues)
- [Roadmap](https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap)
- [Changelog](https://github.com/ArchiveBox/ArchiveBox/wiki/Changelog)
- [Donations](https://github.com/ArchiveBox/ArchiveBox/wiki/Donations)
- [Background & Motivation](https://github.com/ArchiveBox/ArchiveBox#background--motivation)
- [Web Archiving Community](https://github.com/ArchiveBox/ArchiveBox/wiki/Web-Archiving-Community)

---

# ArchiveBox Development

All contributions to ArchiveBox are welcomed! Check our [issues](https://github.com/ArchiveBox/ArchiveBox/issues) and [Roadmap](https://github.com/ArchiveBox/ArchiveBox/wiki/Roadmap) for things to work on, and please open an issue to discuss your proposed implementation before working on things! Otherwise we may have to close your PR if it doesn't align with our roadmap.

### Setup the dev environment

#### 1. Clone the main code repo (making sure to pull the submodules as well)

```bash
git clone --recurse-submodules https://github.com/ArchiveBox/ArchiveBox
cd ArchiveBox
git checkout dev  # or the branch you want to test
git submodule update --init --recursive
git pull --recurse-submodules
```

#### 2. Option A: Install the Python, JS, and system dependencies directly on your machine

```bash
# Install ArchiveBox + python dependencies
python3 -m venv .venv && source .venv/bin/activate && pip install -e '.[dev]'
# or: pipenv install --dev && pipenv shell

# Install node dependencies
npm install

# Check to see if anything is missing
archivebox --version
# install any missing dependencies manually, or use the helper script:
./bin/setup.sh
```

#### 2. Option B: Build the docker container and use that for development instead

```bash
# Optional: develop via docker by mounting the code dir into the container
# if you edit e.g. ./archivebox/core/models.py on the docker host, runserver
# inside the container will reload and pick up your changes
docker build . -t archivebox
docker run -it --rm archivebox version
docker run -it --rm -p 8000:8000 \
    -v $PWD/data:/data \
    -v $PWD/archivebox:/app/archivebox \
    archivebox server 0.0.0.0:8000 --debug --reload
```

### Common development tasks

See the `./bin/` folder and read the source of the bash scripts within.
You can also run all these in Docker. For more examples see the Github Actions CI/CD tests that are run: `.github/workflows/*.yaml`.

#### Run the linters

```bash
./bin/lint.sh
```
(uses `flake8` and `mypy`)

#### Run the integration tests

```bash
./bin/test.sh
```
(uses `pytest -s`)

#### Make migrations or enter a django shell

```bash
cd archivebox/
./manage.py makemigrations

cd path/to/test/data/
archivebox shell
```
(uses `pytest -s`)

#### Build the docs, pip package, and docker image

```bash
./bin/build.sh

# or individually:
./bin/build_docs.sh
./bin/build_pip.sh
./bin/build_deb.sh
./bin/build_brew.sh
./bin/build_docker.sh
```

#### Roll a release

```bash
./bin/release.sh

# or individually:
./bin/release_docs.sh
./bin/release_pip.sh
./bin/release_deb.sh
./bin/release_brew.sh
./bin/release_docker.sh
```

---

<div align="center">
<br/><br/>
<img src="https://raw.githubusercontent.com/Monadical-SAS/redux-time/HEAD/examples/static/jeremy.jpg" height="40px"/>
<br/>
<sub><i>This project is maintained mostly in <a href="https://nicksweeting.com/blog#About">my spare time</a> with the help from generous contributors and Monadical.com.</i></sub>
<br/><br/>

<br/>
<a href="https://github.com/sponsors/pirate">Sponsor us on Github</a>
<br>
<br>
<a href="https://www.patreon.com/theSquashSH"><img src="https://img.shields.io/badge/Donate_to_support_development-via_Patreon-%23DD5D76.svg?style=flat"/></a>
<br/>

<a href="https://twitter.com/ArchiveBoxApp"><img src="https://img.shields.io/badge/Tweet-%40ArchiveBoxApp-blue.svg?style=flat"/></a>
<a href="https://github.com/ArchiveBox/ArchiveBox"><img src="https://img.shields.io/github/stars/ArchiveBox/ArchiveBox.svg?style=flat&label=Star+on+Github"/></a>

<br/><br/>

</div>
