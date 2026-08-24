# Radarr

[![Build Status](https://dev.azure.com/Radarr/Radarr/_apis/build/status/Radarr.Radarr?branchName=develop)](https://dev.azure.com/Radarr/Radarr/_build/latest?definitionId=1&branchName=develop)
[![Translation status](https://translate.servarr.com/widget/servarr/radarr/svg-badge.svg)](https://translate.servarr.com/engage/servarr/?utm_source=widget)
[![Docker Pulls](https://img.shields.io/docker/pulls/linuxserver/radarr.svg)](https://wiki.servarr.com/radarr/installation/docker)
![Github Downloads](https://img.shields.io/github/downloads/Radarr/Radarr/total.svg)
[![Backers on Open Collective](https://opencollective.com/Radarr/backers/badge.svg)](#backers)
[![Sponsors on Open Collective](https://opencollective.com/Radarr/sponsors/badge.svg)](#sponsors)
[![Mega Sponsors on Open Collective](https://opencollective.com/Radarr/megasponsors/badge.svg)](#mega-sponsors)

Radarr is a movie collection manager for Usenet and BitTorrent users. It can monitor multiple RSS feeds for new movies and will interface with clients and indexers to grab, sort, and rename them. It can also be configured to automatically upgrade the quality of existing files in the library when a better quality format becomes available.
Note that only one type of a given movie is supported. If you want both a 4k version and 1080p version of a given movie you will need multiple instances.

## Cook build showcase

Cook produces a real Radarr Linux release archive with one command:

```sh
cook release-linux-x64
# build/release/Radarr.linux-core-x64.tar.gz
```

This is not a parallel reimplementation of Radarr's release process. Cook calls the existing `build.sh --backend` production build, gates packaging on the existing frontend lint and style checks, then calls `build.sh --packages` and archives its output. Cook adds the dependency graph, content-addressed caching, and an explanation for every hit or rebuild.

### Build from a clean clone

Prerequisites are Git, `curl`, `tar`, Perl, Node 20 or newer, and the .NET SDK 8.0.421 pinned by [`global.json`](global.json). On Linux or macOS, install Cook and the published modules, then build:

```sh
curl -fsSL https://getcook.sh | sh
git clone --branch develop https://github.com/LioraLabs/radarr-cook.git
cd radarr-cook
cook modules install
cook release-linux-x64
```

`cook modules install` realizes the exact published module versions in [`cook.lock`](cook.lock), including pnpm 10.33.0. Extract and run the result with isolated application data:

```sh
release_dir=$(mktemp -d)
tar -xzf build/release/Radarr.linux-core-x64.tar.gz -C "$release_dir"
radarr_data=$(mktemp -d)
"$release_dir/Radarr/Radarr" -nobrowser -data="$radarr_data"
# Stop Radarr, then: rm -rf "$release_dir" "$radarr_data"
```

Only `linux-x64` has been executed and verified. This showcase makes no claim yet for Radarr's Windows, macOS, or FreeBSD release targets.

### What changes rebuild

Verified on the final graph:

- The production backend took about 28–49 seconds, depending on the run; packaging and archiving took about 10 seconds.
- An unchanged full release took about 7.2 seconds wall-clock, with Cook reporting about 0.71 seconds of graph execution. `cook menu` took about 1.55 seconds.
- A C# edit reran the production backend and archive while `pnpm:build` and `verify` remained cached.
- A frontend edit left the production backend cached and reran `pnpm:build`, `verify`, and the archive.
- A lint failure prevented the archive step from running.

Use `cook why release-linux-x64` for the inputs, seals, and cache status behind the result. Source snapshots, the `dotnet` toolchain, the release environment, and the locked module versions are all determinants; changing them invalidates the affected work instead of silently reusing it.

The remaining warm-path overhead is executor validation even when the production backend result is reusable. That is tracked as COOK-561; it does not change the artifact or invalidation behavior above.

### Debug developer build

`cook build` is a separate, faster developer path. It builds the .NET workspace in Debug, builds the UI, and assembles a runnable tree under `build/radarr`:

```sh
cook build
```

The [`Cookfile`](Cookfile) uses the published modules directly, with no inline project graph or private module extension.

## Major Features Include

* Adding new movies with lots of information, such as trailers, ratings, etc.
* Support for major platforms: Windows, Linux, macOS, Raspberry Pi, etc.
* Can watch for better quality of the movies you have and do an automatic upgrade. _eg. from DVD to Blu-Ray_
* Automatic failed download handling will try another release if one fails
* Manual search so you can pick any release or to see why a release was not downloaded automatically
* Full integration with SABnzbd and NZBGet
* Automatically searching for releases as well as RSS Sync
* Automatically importing downloaded movies
* Recognizing Special Editions, Director's Cut, etc.
* Identifying releases with hardcoded subs
* Identifying releases with AKA movie names
* SABnzbd, NZBGet, QBittorrent, Deluge, rTorrent, Transmission, uTorrent, and other download clients are supported and integrated
* Full integration with Kodi and Plex (notifications, library updates)
* Importing Metadata such as trailers or subtitles
* Adding metadata such as posters and information for Kodi and others to use
* Advanced customization for profiles, such that Radarr will always download the copy you want
* A beautiful UI

## Support

[![Wiki](https://img.shields.io/badge/servarr-wiki-181717.svg?maxAge=60)](https://wiki.servarr.com/radarr)
[![Discord](https://img.shields.io/badge/discord-chat-7289DA.svg?maxAge=60)](https://radarr.video/discord)

Note: GitHub Issues are for Bugs and Feature Requests Only

[![GitHub - Bugs and Feature Requests Only](https://img.shields.io/badge/github-issues-red.svg?maxAge=60)](https://github.com/Radarr/Radarr/issues)

## Contributors & Developers

[API Documentation](https://radarr.video/docs/api/)

This project exists thanks to all the people who contribute.
- [Contribute (GitHub)](CONTRIBUTING.md)
- [Contribution (Wiki Article)](https://wiki.servarr.com/radarr/contributing)

[![Contributors List](https://opencollective.com/Radarr/contributors.svg?width=890&button=false)](https://github.com/Radarr/Radarr/graphs/contributors)

## Backers

Thank you to all our backers! 🙏 [Become a backer](https://opencollective.com/Radarr#backer)

[![Backers List](https://opencollective.com/Radarr/backers.svg?width=890)](https://opencollective.com/Radarr#backer)

## Sponsors

Support this project by becoming a sponsor. Your logo will show up here with a link to your website. [Become a sponsor](https://opencollective.com/Radarr#sponsor)

[![Sponsors List](https://opencollective.com/Radarr/sponsors.svg?width=890)](https://opencollective.com/Radarr#sponsor)

## Mega Sponsors

[![Mega Sponsors List](https://opencollective.com/Radarr/tiers/mega-sponsor.svg?width=890)](https://opencollective.com/Radarr#mega-sponsor)

## JetBrains

Thank you to [<img src="https://resources.jetbrains.com/storage/products/company/brand/logos/jetbrains.png" alt="JetBrains" width="96">](http://www.jetbrains.com/) for providing us with free licenses to their great tools.

* [<img src="https://resources.jetbrains.com/storage/products/company/brand/logos/ReSharper_icon.png" alt="ReSharper" width="32"> ReSharper](http://www.jetbrains.com/resharper/)
* [<img src="https://resources.jetbrains.com/storage/products/company/brand/logos/WebStorm_icon.png" alt="WebStorm" width="32"> WebStorm](http://www.jetbrains.com/webstorm/)
* [<img src="https://resources.jetbrains.com/storage/products/company/brand/logos/Rider_icon.png" alt="Rider" width="32"> Rider](http://www.jetbrains.com/rider/)
* [<img src="https://resources.jetbrains.com/storage/products/company/brand/logos/dotTrace_icon.png" alt="dotTrace" width="32"> dotTrace](http://www.jetbrains.com/dottrace/)

## DigitalOcean

This project is also supported by DigitalOcean
<p>
  <a href="https://www.digitalocean.com/">
    <img src="https://opensource.nyc3.cdn.digitaloceanspaces.com/attribution/assets/SVG/DO_Logo_horizontal_blue.svg" width="201px">
  </a>
</p>

### License

* [GNU GPL v3](http://www.gnu.org/licenses/gpl.html)
* Copyright 2010-2025
