---
layout: post
title:  "The Search for Reliable SSH"
author: wrideout
date:   2016-04-11 20:12:41 -0700
<<<<<<< HEAD
tags: [tools, vim, osx]
=======
categories: til tools vim osx
>>>>>>> 9e1967da731c6fe71ce94258e93705bf8b14f76a
---

## Imagine this scenario...

... you work for a development group that has a large, complicated, over-the-top
build system on a remote machine that is utterly necessary to build _The
Product_.  What's more, your codebase is huge; way too big to put on your poor
little Macbook.  It looks like editing code remotely on the build machine is
going to be the easiest path to success.  So, you fire up several SSH sessions,
and get coding.  Ten minutes later, you switch back to the shell that had your
build results in it.  Huh, that's weird; it must have timed out due to
inactivity.  

While the SSH timeout isn't a big deal if you only run one session, or even a
couple, it can get *very* annoying when you run as many as I do!  I quickly
became fed up with having to reconnect my SSH session over and over and *over*
again.  I needed a better solution!

## Enter, Mosh

[Mosh](https://mosh.mit.edu/) is a neat program from the clever folks over at
MIT.  The basic idea is that Mosh is a replacement for SSH that is persistent.
Connections formed using Mosh are able to survive network drops, and even
__roams__.  That's right, you can even switch networks without losing your
remote connection.  Cool stuff, right?

Initially, I was thrilled with Mosh; it provided the exact feature I was looking
for.  It wasn't long, however, until I found a shortcoming.  The build system I
use at work prints a _ton_ of output to the console.  Much of it is
uninteresting, but sometimes failure messages aren't printed in the last
screen's-worth of text.  As it turns out, Mosh behaves much like telnet used (?)
to: output that is pushed beyond the upper bounds of the terminal window is
abandoned.  Simply put, I didn't have enough scrollback space in order to see
the messages I needed.  I now needed a way to extend this scrollback.  Now,
there is a bug filed for Mosh that requests this feature, and in fact extended
scrollback is slated as a feature for the 1.3 release of Mosh.  I was, as you
can imagine, unwilling to wait for a new release.  I needed a workaround.

## Tmux to the rescue

Googling around showed that I was not the only one out there looking for a
solution to the Mosh scrollback problem.  I played around with several options,
ultimately settling on using [Tmux](https://tmux.github.io/) to manage the
scrollback, among other things.  If you're not familiar with Tmux, it's
essentially the BSD version of screen.  There is a raging debate out in the
Internet as to which is the better application.  Feel free to draw your own
conclusions; I'll provide links to all my resources at the bottom.  I've found
Tmux to be adequate for my needs, albeit with a little configuration.

All configuration happens in a file in your home directory, named ".tmux.conf".  
First of all, I wanted to start with a new session, and make sure I had a good
long scrollback to work with.  I'm also a Vim user, and wanted to use Vim-style
keybindings inside Tmux.  Do do all that, I put the following in my configuration
file:

```
new-session
set -g history-limit 30000
setw -g mode-keys vi
```

I tested my configuration, and I had scrollback!  I could execute a lengthy
command, such as `ps ax` on my remote server and scroll back over the output to
my heart's content.  There was one hangup, though: I could only scroll using the
keybindings in Tmux.  I wanted to be able to scroll using my Magic Trackpad (a
wonderful piece of gadgetry).  So, I did some more googling.  I found a project
on Github that provides exactly this functionality, right
[here](https://github.com/NHDaly/tmux-scroll-copy-mode).  I followed the
directions in the project's README by cloning the repository, and adding the
following to my Tmux config:

```
setw -g mouse on
run-shell PATH/TO/scroll_copy_mode.tmux
```

Tested the updated config... and success!  I could now scroll using my trackpad.
Happy, I started up a new session, and began editing code, only to find that my
Vim syntax colors were a little off.  As it turns out, one more line was
required to fix the colors:

```
set -g default-terminal "screen-256color"
```

## Conclusion

So, altogether that's not that bad configuration-wise.  Mosh did not require any
further configuration to work, and all told my entire Tmux
configuration file is six lines.  Here's
all the configuration details in its entirety:

```
new-session
set -g history-limit 30000
setw -g mode-keys vi
setw -g mouse on
run-shell PATH/TO/scroll_copy_mode.tmux
set -g default-terminal "screen-256color"
```

Just to make using this new setup easier, I also created an alias that allows me
to connect to my remote machine and enter the last-used Tmux session.  I keep
this in my .zshrc, although you can substitute any shell you wish.

```
alias bs='mosh USER@HOST -- tmux a'
```

Not bad for an afternoon's worth of googling!

### Resources

* [Mosh](https://mosh.mit.edu/)
* [Tmux](https://tmux.github.io/)
* [Getting 256-Colors to Work in Tmux](https://unix.stackexchange.com/questions/1045/getting-256-colors-to-work-in-tmux)
* [Better Mouse Scrolling in Tmux](https://www.davidverhasselt.com/better-mouse-scrolling-in-tmux/)
<<<<<<< HEAD
=======

>>>>>>> 9e1967da731c6fe71ce94258e93705bf8b14f76a
