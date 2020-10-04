# Tutorial

Although Rib is at its core a Haskell library (and meant to be used as one, rather than as a framework), it provides toolset (based on nix, ghcid, fsnotify, etc.) to make working with static sites pleasant.

## Directory structure

Let us clone the template repository, [**rib-sample**](https://github.com/srid/rib-sample), and inspect what's in it:

```bash
$ git clone https://github.com/srid/rib-sample.git mysite
...
$ cd mysite
$ ls -F
content/  default.nix  Main.hs  README.md  rib-sample.cabal
```

The three key items here are:

1. `Main.hs`: Haskell source containing the DSL of the HTML/CSS of your site.
   See [[preview]].
2. `content/`: The source content (eg: Markdown sources and static files)
3. `dest/`: The target directory, excluded from the git repository, will contain
   _generated_ content (i.e., the HTML files, and copied over static content)
   
The template repository comes with a few sample posts under `content/`, and a basic
HTML layout and CSS style defined in `Main.hs`. 

## Run the site

Now let's run them all. 

Clone the sample repository locally, install [Nix](https://nixos.org/nix/) (as
described in its README) and run your site as follows:

```bash
nix-shell --run 'ghcid -T ":main serve"'
```

Running this command gives you a local HTTP server at <http://127.0.0.1:8080>
(serving the generated files) that automatically reloads when either the content
(`content/`) or the HTML/CSS/build-actions (`Main.hs`) changes. Hot reload, in other
words.

## How Rib works

How does the aforementioned nix-shell command work?

1. `nix-shell` will run the given command in a shell environment with all of our
dependencies (notably the Haskell ones including the `rib` library itself)
installed. 

2. [`ghcid`](https://github.com/ndmitchell/ghcid) will compile your `Main.hs`
   and run its `main` function.

3. `Main.hs:main` in turn calls `Rib.App.run` which takes as argument your custom 
   Shake action that will build the static site.

4. `Rib.App.run`: this parses the CLI arguments and runs the rib CLI "app" which
   can be run in one of a few modes --- generating static files, watching the
   `content/` directory for changes, starting HTTP server for the `dest/` directory.
   The "serve" subcommand will run the Shake build action passed as argument on 
   every file change and spin up a HTTP server.
   
Run that command, and visit <http://127.0.0.1:8080> to view your site.

## Editing workflow

Now try making some changes to the content, say `content/first-post.md`. You should
see it reflected when you refresh the page. Or change the HTML or CSS of your
site in `Main.hs`; this will trigger `ghcid` to rebuild the Haskell source and
restart the server.

## What's next?

Great, by now you should have your static site generator ready and running! 

Rib recommends writing your Shake actions in the style of being 
[forward-defined](http://hackage.haskell.org/package/shake-0.18.3/docs/Development-Shake-Forward.html)
which adds to the simplicity of the entire thing.

