The Bash prompt is a vital part of the Linux command line, and it can do a lot of useful things. But it can be difficult to get started on customising it.

In this video, I'll show you how to make your prompt easier to both understand and modify. We'll go from something like this:
```
${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$
```
to something like this:
```
\[$bold$fg_green\]\u@\h\[$reset\]:\[$bold$fg_blue\]\w\[$reset\]\$
```

*and* you'll understand what every part of the definition does and how you can change it in future.

This is Linux Dinosaur. I've been a using Linux for fun and profit for three decades, and this channel is where I share useful things I've learned in that time. Welcome.

So let's take a look at the default prompt on Ubuntu. *Username at host* in green, working directory in blue. Small and simple.

But look at its definition, which stored in the PS1 variable:
```
$ echo $PS1
${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$
```

For such a small simple prompt, this is big and hard to understand. It's doing more than you probably want.

This first part, for instance, is only useful when using Debian chroot. It's not a visible component of the prompt under normal circumstances.

This is the part that defines what you actually see, and it's a mess of numbers and punctuation. It's not obvious how to make even a simple change, like "make the green part red"

So let's fix that.

By default, the prompt is defined in my user's .bashrc file, in this section here.

I'll leave all this default configuration in place for now - it makes it easier to revert if I need to.

I'll just add a call to a prompt-specific file at the bottom of the file
```
source ~/.bash_prompt
```

Then I'll use the non-coloured default prompt definition as my starting place, since it's the simplest.

I'm not going to keep the chroot part - I haven't used chroot since I bootstrapped Linux From Scratch, it's been largely superseded by containers.

I'm also not going to keep the window title-setting behaviour. I just want the prompt elements themselves.

So my starting place becomes this very simple definition
```
PS1='\u@\h:\w\$ '
```

And I can show you how that looks by pasting it directly into an existing shell.

As you can see, this gives us a colour-free version of the default prompt. Hopefully it's simple enough that it's easy for you to understand what the different parts of the definition are doing - username at host, colon, working directory, symbol, space

But I do like a coloured prompt, so I want to put that configuration in too. But I want it in a human-readable way.

To avoid the ugliness we saw earlier, I've used Bash's `printf -v` to put formatting codes into shell variables. This is all in a separate file I'll call that allows me to get the same prompt formatting in a much more legible way:
```
source ~/.bash_prompt_vars
PS1='${bold}${fg_green}\u@\h${reset}:${bold}${fg_blue}\w${reset}\$ '
```

Again, I can paste this right into the terminal to preview it, and it gets us back to the original green-and-blue prompt behaviour. Looks good.

But there is a problem with this definition. Let me show you what happens when I start scrolling around through my history.

The problem here is that instructions like colour codes don't take up any space on the terminal, so we need to specifically flag them as non-printing sequences. Otherwise this kind of confusion will persist.

To do this, we have to bring back a few of the square brackets we saw in the original definition. But these don't harm legibility too badly, in my view.
```
PS1='\[$bold$fg_green\]\u@\h\[$reset\]:\[$bold$fg_blue\]\w\[$reset\]\$ '
```

And now when I scroll through history and move around in the command, everything behaves.

So let's save this in our `.bash_prompt` file along with the call to the colour definitions:
```
source ~/.bash_prompt_vars
PS1='\[$bold$fg_green\]\u@\h\[$reset\]:\[$bold$fg_blue\]\w\[$reset\]\$ '
```

Compared to the original definition, I hope you agree that this is easier to both read and edit. If I want to make a simple change like red text instead of green, it's as easy as
```
PS1='\[$bold$fg_red\]\u@\h\[$reset\]:\[$bold$fg_blue\]\w\[$reset\]\$ '
```
