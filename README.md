# Overview

This repo includes a simple way to install some language servers that might work
with YouCompleteMe (strictly ycmd). 

This repo comes with no warranty, and these engines are not officially supported
by YCM, though they should work for the most part.

# Languages Tested

Working:

* D
* Dockerfile
* Kotlin
* Ruby
* Vue
* Vim (vimscript)

Broken or partially working:

* JSON
* PHP
* YAML

See also:

* [C-family with ccls](https://github.com/MaskRay/ccls/wiki/YouCompleteMe)

# Quick start

Assuming you installed this repo in `$HOME/Development/lsp`:

* For each of the servers you want, run the `install` script in that directory.
* Add the following to your `vimrc` (remove any that you didn't install):

```viml
let g:ycm_language_server = [
  \   {
  \     'name': 'yaml',
  \     'cmdline': [ 'node', expand( '$HOME/Development/lsp/yaml/node_modules/.bin/yaml-language-server' ), '--stdio' ],
  \     'filetypes': [ 'yaml' ],
  \   },
  \   {
  \     'name': 'php',
  \     'cmdline': [ 'php', expand( '$HOME/Development/lsp/php/vendor/bin/php-language-server.php' ) ],
  \     'filetypes': [ 'php' ],
  \   },
  \   {
  \     'name': 'json',
  \     'cmdline': [ 'node', expand( '$HOME/Development/lsp/json/node_modules/.bin/vscode-json-languageserver' ), '--stdio' ],
  \     'filetypes': [ 'json' ],
  \   },
  \   {
  \     'name': 'ruby',
  \     'cmdline': [ expand( '$HOME/Development/lsp/ruby/bin/solargraph' ), 'stdio' ],
  \     'filetypes': [ 'ruby' ],
  \   },
  \   { 'name': 'kotlin',
  \     'filetypes': [ 'kotlin' ], 
  \     'cmdline': [ expand( '$HOME/Development/lsp/kotlin/server/build/install/server/bin/server' ) ],
  \   },
  \   { 'name': 'd',
  \     'filetypes': [ 'd' ], 
  \     'cmdline': [ expand( '$HOME/Development/lsp/d/serve-d' ) ],
  \   },
  \   { 'name': 'vue',
  \     'filetypes': [ 'vue' ], 
  \     'cmdline': [ expand( '$HOME/Development/lsp/vue/node_modules/.bin/vls' ) ]
  \   },
  \   { 'name': 'docker',
  \     'filetypes': [ 'dockerfile' ], 
  \     'cmdline': [ expand( '$HOME/Development/lsp/docker/node_modules/.bin/docker-langserver' ), '--stdio' ]
  \   },
  \   { 'name': 'vim',
  \     'filetypes': [ 'vim' ],
  \     'cmdline': [ expand( '$HOME/Development/lsp/viml/node_modules/.bin/vim-language-server' ), '--stdio' ]
  \   },
  \   { 'name': 'scala',
  \     'filetypes': [ 'scala' ],
  \     'cmdline': [ 'metals-vim' ],
  \     'project_root_files': [ 'build.sbt' ]
  \   },
  \   { 'name': 'purescript',
  \     'filetypes': [ 'purescript' ],
  \     'cmdline': [ expand( '$HOME/Development/lsp/viml/node_modules/.bin/purescript-language-server' ), '--stdio' ]
  \   },
  \   { 'name': 'fortran',
  \     'filetypes': [ 'fortran' ],
  \     'cmdline': [ 'fortls' ],
  \   },
  \   { 'name': 'haskell',
  \     'filetypes': [ 'haskell', 'hs', 'lhs' ],
  \     'cmdline': [ 'hie-wrapper' ],
  \     'project_root_files': [ '.stack.yaml', 'cabal.config', 'package.yaml' ]
  \   },
  \   { 'name': 'julia',
  \     'filetypes': [ 'julia' ],
  \     'project_root_files': [ 'Project.toml' ],
  \     'cmdline': <See note below>
  \   }
  \ ]
```

* Adjust the directory as appropriate

* **NOTE**: YCM will regard the path of `.ycm_extra_conf.py` as root path of project folder.
So please make sure you put your `.ycm_extra_conf.py` at right place (root of current project)

# Purescript

Ycmd currently doesn't support `showMessageRequest`, so users need to manually build their projects
on the command line before starting the server. To do this execute `pulp build` in the project root.

# Scala

Ycmd currently doesn't support `showMessageRequest`, so users need to "import build" manually.
Unlike purescript, for scala, this can be done in the editor by executing
`:YcmCompleter ExecuteCommand build-import`. For this operation to succeed `sbt` and `bloop`
need to be in the `$PATH`. `metals` also requires java 8.

For completions to work make sure the version of `metals` has [this bug fix][metals-pr].

# Haskell

Currently haskell-ide-engine always completes snippets, which trips up ycmd.
The [pull request][hie-pr] that fixed this bug has already been merged, but isn't
available in version 0.13.0.0. Until the next version of HIE compile its git master.

The HIE install instructions can be found [here][hie-install].

HIE also requires a `.ycm_extra_config.py` with the following content:

```python
def Settings(**kwargs):
  return { 'ls': { "languageServerHaskell": {} } }
```

# Fortran

The server causes a spurious error:

- `fortls` doesn't support `didChangeConfiguration`.

This error can be ignored, as they don't interfere with normal work of ycmd/fortls.

# Ruby

You need to be running a version of ruby that the parser understands:
https://github.com/whitequark/parser#compatibility-with-ruby-mri

Recommend running in [rbenv][] for that:

```
$ rbenv shell 2.3.8
$ cd ruby
$ ./install
$ vim test/test.rb
```

# D

There is a number of external dependencies that you will want to install:

- `libphobos`/`liblphobos` - the D standard library
- `dmd` - the D compiler
- `dscanner` - at the very least responsible for diagnostics
- `dcd` - the D compiler daemon
- Potentially `dfmt` - `serve-d` seems to be able to format code even without it.
- `dub` - the D package manager

On top of that, you will want to configure the server, at least to let `serve-d`
know about your modules. The configuration is done through ycmd's extra confs
and the full list of `serve-d`'s configuration options can be found
[here][d-conf].

Note that the server executable on Windows is called `serve-d.exe`.

# Kotlin

For whatever reason, the server expects you to have maven in your `PATH` and,
just like `serve-d`, `kotlin-language-server` has its own [configuration][kt-conf].

The server executable is actually a shell script and the build process produces
`server` for Linux and `server.bat` for Windows.

Make sure to put a `.ycm_extra_conf.py` file in the root of your project, otherwise
[the language server may fail][kt-issue].

# Julia

The command line for starting the server is:

```viml
let g:julia_cmdline = ['julia', '--startup-file=no', '--history-file=no', '-e', '
\       using LanguageServer;
\       using Pkg;
\       import StaticLint;
\       import SymbolServer;
\       env_path = dirname(Pkg.Types.Context().env.project_file);
\       debug = false;
\
\       server = LanguageServer.LanguageServerInstance(stdin, stdout, debug, env_path, "", Dict());
\       server.runlinter = true;
\       run(server);
\   ']
```

You can replace the first command line argument (`'julia'`) with an absolute
path, if `julia` isn't in your `$PATH`.
With the above list in your vimrc, you can set `'cmdline'` in
`g:ycm_language_server` to just `g:julia_cmdline`.

Julia server *does* support configuration via the extra conf, but it doesn't
seem to be documented anywhere.

# Known Issues

- `yaml` completer completions don't work because the server [bugs][yaml-bug]
  always returns snippets, even though ycmd claims not to support them.
  Validation works though.
- `json` completer completions don't work because the server [bugs][json-bug]
  always returns snippets, even though ycmd claims not to support them.
  Validation works though.
- `php` completer generally never works. It just seems broken.


[yaml-bug]: https://github.com/redhat-developer/yaml-language-server/issues/161
[json-bug]: https://github.com/vscode-langservers/vscode-json-languageserver-bin/issues/2
[rbenv]: https://github.com/rbenv/rbenv
[d-conf]: https://github.com/Pure-D/serve-d/blob/master/source/served/types.d#L64
[kt-conf]: https://github.com/fwcd/KotlinLanguageServer/blob/master/server/src/main/kotlin/org/javacs/kt/KotlinWorkspaceService.kt#L81
[kt-issue]: https://github.com/ycm-core/lsp-examples/issues/5
[hie-pr]: https://github.com/haskell/haskell-ide-engine/pull/1424
[hie-install]: https://github.com/haskell/haskell-ide-engine#installation
[metals-pr]: https://github.com/scalameta/metals/issues/1057
