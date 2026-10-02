# mimir-tables

League of Legends hash tables in the `.lhdb` format of
[mimir](https://github.com/LeagueToolkit/mimir), published as GitHub releases.

This repository holds no code and no tables in git. A scheduled workflow builds the tables
from the hash lists in [CommunityDragon/Data](https://github.com/CommunityDragon/Data) and
publishes them as a `hashes-<version>` release. The version is the build date in UTC
(`YYYY-MM-DD`) unless a manual run sets it.

## Download

The current tables are always the latest release:

```
https://github.com/LeagueToolkit/mimir-tables/releases/latest/download/<file>
```

Each release contains:

| File                     | Contents                                                                  |
| ------------------------ | ------------------------------------------------------------------------- |
| `manifest.json`          | Filename, sha256, entry count, format version and key width of each table |
| `manifest-v<n>.json`     | The same manifest for readers of `.hashdb` format version `n`             |
| `<table>-<version>.lhdb` | One table                                                                 |

| Table        | Source list             | Names                   |
| ------------ | ----------------------- | ----------------------- |
| `game`       | `hashes.game.txt`       | Game WAD paths          |
| `lcu`        | `hashes.lcu.txt`        | League client WAD paths |
| `binentries` | `hashes.binentries.txt` | Bin entry names         |
| `bintypes`   | `hashes.bintypes.txt`   | Bin class names         |
| `binfields`  | `hashes.binfields.txt`  | Bin field names         |
| `binhashes`  | `hashes.binhashes.txt`  | Bin hash values         |
| `rst`        | `hashes.rst.xxh64.txt`  | RST string keys, XXH64  |
| `rst-xxh3`   | `hashes.rst.xxh3.txt`   | RST string keys, XXH3   |

Only the latest release is kept. Download the manifest and the tables from the same
release, because the table filenames change with every version.

## Use

With [`ltk_mimir_cache`](https://crates.io/crates/ltk_mimir_cache):

```rust
use ltk_mimir_cache::{HashStore, ReleaseSource, UpdateOptions, UreqFetch};

let store = HashStore::discover()?;
let remote = UreqFetch::new(ReleaseSource::github("LeagueToolkit/mimir-tables"));
store.update(&remote, UpdateOptions::default())?;
```

With the `mimir` CLI:

```
mimir update --repo LeagueToolkit/mimir-tables
```

## Publishing

[`.github/workflows/release.yml`](.github/workflows/release.yml) runs every Monday at
04:17 UTC. It compares the source repository and commit recorded in the latest manifest
with the current `HEAD` of the source, and publishes nothing when they match.

To publish by hand, run the **Release tables** workflow from the Actions tab or with:

```
gh workflow run release.yml --repo LeagueToolkit/mimir-tables
```

| Input         | Default                | Purpose                                                |
| ------------- | ---------------------- | ------------------------------------------------------ |
| `version`     | today, UTC             | Version label in the tag and the table filenames       |
| `source_repo` | `CommunityDragon/Data` | GitHub `owner/repo` or git URL with `hashes/lol/*.txt` |
| `mimir_ref`   | `main`                 | Branch, tag or commit of mimir to build the CLI from   |
| `force`       | `false`                | Publish even when the source commit is unchanged       |

Use `force` after a change to mimir's table builder, because the source commit alone does
not show it.

## License

The files in this repository are licensed under the Apache License, Version 2.0
([LICENSE](LICENSE)). The tables are built from the hash lists in
[CommunityDragon/Data](https://github.com/CommunityDragon/Data).
