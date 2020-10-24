# bupstash

bupstash is a secure data store for uses ranging from business critical data archiving,
to small scale personal data collections. 

Some key features:

- Secure offline keys, data can't be decrypted without them, but new backups can still be made.

- Data deduplication allowing efficient storage of large numbers of data snapshots.

- Easy incremental backups without the headache.

- Client side encryption of data and metadata, cryptographically assured privacy.

- Access controls over ssh, allowing different permissions on a per ssh key basis.

- Key/Value backup tagging system and simple query language.

- Simple, scriptable command line interface.

- Self hosting with nothing more than an ssh server.

- Low ram usage and high performance.

- Append only access controls for high security deployments.

- Written in rust to mitigate many classes of security bugs.

*WARNING* Bupstash is alpha software, backwards incompatible changes may be made.
While all efforts are made to keep bupstash bug free, we currently recommend
using bupstash for making *REDUNDANT* backups where failure can be tolerated.

# Typical usage

Initialize a new bupstash repository via ssh.
```
$ export BUPSTASH_REPOSITORY=ssh://$SERVER/home/me/backups
$ bupstash init
```

Create a new encryption key, and tell bupstash to use it.
```
$ bupstash new-key -o backups.key
$ export BUPSTASH_KEY="$(pwd)/backups.key"
```

Save a directory as a tarball snapshot.
```
$ bupstash put hostname="$(hostname)" ./some-data
ebb66f3baa5d432e9f9a28934888a23d
```

Save a file, with arbitrary tag/value tags.
```
$ bupstash put mytag=myvalue ./some-file.txt
bcb8684e6bf5cb453e77486decf61685
```

Save the output of a command, checking for errors.
```
$ bupstash put --exec name=database.sql pgdump mydatabase
14ebd2073b258b1f55c5bbc889c49db4
```

List items matching a query.
```
$ bupstash list name="backup.tar" and hostname="server-1"
id="bcb8684e6bf5cb453e77486decf61685" name="some-file.txt" hostname="black" timestamp="2020/07/27 11:26:16"
```

Get an item matching a query.
```
$ bupstash get id=bcb8684e6bf5cb453e77486decf61685
some data...

$ bupstash get id="ebb66*" | tar -C ./restore -xf -
```

Remove items matching a query.
```
$ bupstash rm name=some-data.txt and older-than 30d
```

Run the garbage collector to reclaim disk space.
```
$ bupstash gc
```

# Installation

## From source

First ensure you have a recent rust+cargo, pkg-config and libsodium-dev package installed.

Next clone the repository and run cargo build.
```
$ git clone https://github.com/andrewchambers/bupstash
$ cd bupstash
$ cargo build --release
$ cp ./target/release/bupstash $INSTALL_DIR
```

## Precompiled releases

Head to the [releases page](https://github.com/andrewchambers/bupstash/releases) and download for 
a build for your platform. Simply extract the archive and add the single bupstash binary to your PATH.

Currently we only precompile for linux (help wanted for more platforms).


# Guides and documentation

Check the [Quickstart guide](./doc/guides/Quick Start.md) for an introductory tutorial.


Check the [Man pages](./doc/man) for more comprehensive documentation.


