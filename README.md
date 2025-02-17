# vim-oscyank

A Vim / Neovim plugin to copy text to the system clipboard from anywhere using
the [ANSI OSC52](https://invisible-island.net/xterm/ctlseqs/ctlseqs.html#h3-Operating-System-Commands)
sequence.

When this sequence is emitted by Vim, the terminal will copy the given text into
the system clipboard. **This is totally location independent**, users can copy
from anywhere including from remote SSH sessions.

The only requirement is that the terminal must support the sequence. Here is a
non-exhaustive list of the status of popular terminal emulators regarding OSC52
(as of June 2022):

| Terminal | OSC52 support |
|----------|:-------------:|
| [Alacritty](https://github.com/alacritty/alacritty) | **yes** |
| [foot](https://codeberg.org/dnkl/foot) | **yes** |
| [GNOME Terminal](https://github.com/GNOME/gnome-terminal) (and other VTE-based terminals) | [not yet](https://bugzilla.gnome.org/show_bug.cgi?id=795774) |
| [hterm (Chromebook)](https://chromium.googlesource.com/apps/libapps/+/master/README.md) | [**yes**](https://chromium.googlesource.com/apps/libapps/+/master/nassh/doc/FAQ.md#Is-OSC-52-aka-clipboard-operations_supported) |
| [iTerm2](https://iterm2.com/) | **yes** |
| [kitty](https://github.com/kovidgoyal/kitty) | **yes** |
| [Konsole](https://konsole.kde.org/) | [not yet](https://bugs.kde.org/show_bug.cgi?id=372116) |
| [screen](https://www.gnu.org/software/screen/) | **yes** |
| [st](https://st.suckless.org/) | **yes** (but needs to be enabled, see [here](https://git.suckless.org/st/commit/a2a704492b9f4d2408d180f7aeeacf4c789a1d67.html)) |
| [Terminal.app](https://en.wikipedia.org/wiki/Terminal_(macOS)) | no, but see [workaround](https://github.com/roy2220/osc52pty) |
| [tmux](https://github.com/tmux/tmux) | **yes** |
| [Windows Terminal](https://github.com/microsoft/terminal) | **yes** |
| [rxvt](http://rxvt.sourceforge.net/) | **yes** (to be confirmed) |
| [urxvt](http://software.schmorp.de/pkg/rxvt-unicode.html) | **yes** (with a script, see [here](https://github.com/ojroques/vim-oscyank/issues/4)) |
| [xterm.js](https://xtermjs.org/) (Hyper terminal) | [not yet](https://github.com/xtermjs/xterm.js/issues/3260) |
| [wezterm](https://github.com/wez/wezterm) | [**yes**](https://wezfurlong.org/wezterm/escape-sequences.html#operating-system-command-sequences) |

Feel free to add terminals to this list by submitting a pull request.

## Installation
With [vim-plug](https://github.com/junegunn/vim-plug):
```vim
Plug 'ojroques/vim-oscyank', {'branch': 'main'}
```

## Basic usage
Enter Visual mode, select your text and run `:OSCYank`.

You may want to map the command:
```vim
vnoremap <leader>c :OSCYank<CR>
```

You can also use the OSCYank operator:
```vim
nmap <leader>o <Plug>OSCYank
```
like so for instance:
```
<leader>o_   " copy the current line
<leader>oip  " copy the inner paragraph
```

## Copying from a register
If you prefer to copy text from a particular register, use:
```vim
:OSCYankReg +  " this will copy to clipboard the text in register '+'
```

To copy after any yank operation:
```vim
autocmd TextYankPost * if v:event.operator is 'y' && v:event.regname is '' | execute 'OSCYankReg "' | endif
```

Or to copy only the `+` register (vim's *system clipboard* register):
```vim
autocmd TextYankPost * if v:event.operator is 'y' && v:event.regname is '+' | execute 'OSCYankReg +' | endif
```

## Configuration
By default you can copy up to 100000 characters at once. If your terminal
supports it, you can raise that limit with:
```vim
let g:oscyank_max_length = 1000000
```

The plugin treats *tmux*, *screen* and *kitty* differently than other terminal
emulators. The plugin should automatically detects the terminal used but you can
bypass detection with:
```vim
let g:oscyank_term = 'tmux'  " or 'screen', 'kitty', 'default'
```

By default a confirmation message is echoed after text is copied. This can be
disabled with:
```vim
let g:oscyank_silent = v:true  " or 1 for older versions of Vim
```

## Troubleshooting

#### The plugin does not work with tmux
First follow this [guide from
tmux](https://github.com/tmux/tmux/wiki/Clipboard). If it still doesn't work,
try setting `set -s set-clipboard on` in your tmux config and put `let
g:oscyank_term = 'default'` in your Vim config (see issue #26).

## Features
There are already Vim plugins implementing OSC52. However this plugin fixes
several issues I've had with them:
* It supports Neovim.
* It supports Windows.
* It does not mandate users to overwrite their unnamed register (`"`).
* It makes the maximum length of strings configurable.
* It supports [kitty](https://sw.kovidgoyal.net/kitty/changelog/#id33) versions
  below `0.22.0` which implement a slightly modified OSC52 protocol.

## Credits
The code is derived from
[hterm's script](https://github.com/chromium/hterm/blob/master/etc/osc52.vim).

[hterm's LICENCE](https://github.com/chromium/hterm/blob/master/LICENSE)<br/>
[vim-oscyank's LICENSE](LICENSE)
