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

This fork can build a runnable Radarr tree with [Cook](https://github.com/LioraLabs/cook). Use a Cook build containing `cook.materialize` and the COOK-558 qualified-recipe resolution fix; the tested main build is `b055a6e9`, which reports Cook 0.16.1 / Standard v0.18. Also use Node 20 or newer, the .NET SDK 8.0.421 pinned by [`global.json`](global.json), and pnpm 10.33.0 (installed by the pnpm module).

From a clean checkout:

```sh
cook modules install
cook build
cook build                 # warm run: all unchanged work is cached
cook why build             # read-only explanation of hits and rebuilds
```

The graph builds every .NET project through `dotnet:build`, builds the UI with `pnpm:build`, then assembles those aggregate outputs into `build/radarr`. Run it with isolated application data:

```sh
radarr_data=$(mktemp -d)
build/radarr/bin/Radarr -nobrowser -data="$radarr_data"
# Stop Radarr, then remove "$radarr_data" when finished.
```

Cache keys follow declared inputs. A frontend edit invalidates `pnpm:build` and the final runnable tree, not the .NET closure; a core C# edit invalidates its affected .NET work and the final tree, not `pnpm:build`. To inspect either case without retaining the test edit:

```sh
(
  file=frontend/src/Diag/ConsoleApi.js # or src/NzbDrone.Core/Languages/Language.cs
  backup=$(mktemp)
  cp "$file" "$backup"
  trap 'cp "$backup" "$file"; rm -f "$backup"' EXIT
  printf '\n' >> "$file"
  cook why build
)
```

The committed [`cook.lock`](cook.lock) supplies the exact tested module closure. `cook_dotnet` 0.3 exposes the generated projects' existing build outputs through `dotnet:build`, so the [`Cookfile`](Cookfile) consumes the workspace aggregate directly. The modules are used as published, with no inline extension.

Fork-local Radarr adaptations are limited to plain pnpm type dependencies, `ArtifactsPath` precedence, rooted StyleCop paths, and Windows targeting support.

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
