---
layout: post
categories: customization linux
---

bspwm
-----
Binary Space Partition Window Manager! Oh how I do adore thee. You let me
arbitrarily divide up the windows on my screen into neat regions that I have
fine control over, and you let me dictate my own arbitrary hotkeys.

In case I miss anything, [bspwm for dummies][1] is a useful guide. Most of
this article is almost copied verbatim from there.

[1]: https://github.com/windelicato/dotfiles/wiki/bspwm-for-dummies

Alright let's get  serious. First, we want to get some basic development tools
installed on our system.

    sudo yum groupinstall "Development Tools"
    sudo yum install automake
    sudo yum install vim-enhanced
    sudo yum install libtool
    sudo yum install gcc-c++

I suggest you dump all of your customization things into a subfolder of your
home directory (~ is shorthand for your home directory).

    cd ~
    mkdir customization
    cd customization

Now, we want the bspwm source code. We'll compile it ourselves. Git will, by
default, clone a remote repo into a new folder named after the repo. Git is a
popular version control system used by a lot of open source projects (it's where
github gets its name). "git clone" makes a copy of the remote repository
(repository being where the source code is stored) with all of its update
history, but the part that's relevant to us right now is that it gives us a copy
of the source we can build.

    git clone https://github.com/Baskerville/bspwm.git

We want to install some dependencies (mostly libraries needed for compiling)
for bspwm:

    yum install libxcb-devel xcb-util-devel xcb-util-wm xcb-util-wm-devel

Compile bspwm with:

    cd bspwm
    make
    sudo make install

"make" refers to GNU make, a part of the GNU project that facilitates compiling
source code. It uses a Makefile, which is a file literally named "Makefile"
that contains commands tell "make" how to build our executables. The
Makefile is provided for us. However, oftentimes for projects that are more
sensitive to our build environment, particularly things like architecture,
operating-system-specific build paths, and other things that might make a build
environment different from the developer's you might have to run ./autogen.sh
and ./configure which makes a Makefile for you).

"make install" isn't actually a special command; it is more of a popular
convention.  "install" is just a *target* of make. When you invoke "make
install", the program make looks for a Makefile in your current directory. If a
Makefile is found, "make" looks for a section of the file that begins with
"install:" and executes the following commands to run the installation process
for this program.

We can see how it's installing if we inspect the Makefile:

    less Makefile

"less" is a program that just prints out a file buffered by line, so you can
scroll up and down within it. Scroll with the arrow keys, page up/down, or --
if you're feeling adventurous -- "hjkl".  "hjkl" correspond to up left, down,
up, and right, respectively. They come from vim, which is a text editor I'll
probably introduce later.

We can also search with "/". While in less, just press /, type some text you
want to match, and press enter. "n" finds the next match, "N" finds the
previous.  These key bindings also come from vim. If you type
"/install<Return>" you'll find the *install* target. Notice that it consists
solely of "mkdir" and "cp", which make directories and copy files into them. In
this case, it's copying the bspwm and bspc (which is used to control bspwm)
exectuables into our /usr/local/bin folder. It also copies some manual pages
and bash-completion stuff into their respective directories. From "install", we
observe that installation usually does not involve much more than copying
executable files into somewhere on our $PATH, which is a list of directories
that your terminal looks through when you enter a command.

We also want sxhkd (a hotkey manager):

    git clone https://github.com/Baskerville/sxhkd.git

And the dependencies for that:

    yum install xcb-util-keysyms xcb-util-keysyms-devel

Build and install sxhkd the same way as bspwm:

    cd sxhkd
    make
    sudo make install

Let's also drop some config files into the appropriate directories:

    mkdir ~/.config/bspwm
    cp bspwm/examples/bspwmrc ~/.config/bspwm/bspwmrc
    chmod +x ~/.config/bspwm/bspwmrc
    mkdir ~/.config/sxhkd
    cp bspwm/examples/sxhkdrc ~/.config/sxhkd/sxhkdrc

"rc" ("run commands") is a common suffix for configuration files executed when
a program is run. There's a [wikipedia article][2] with some background. Try
reading up on things you don't know on Wikipedia. It usually has some
interesting history, relevant usage information, and things of that nature.

You may notice at the bottom of sxhkdrc that there are shortcuts for opening
various programs. I personally find these very, very useful. Super + Enter
opens up a terminal. Super + Space opens up dmenu, which is sort of like the
Super + r "Run..." menu from Windows that you may be familiar with. You may
want to edit the terminal entry to say gnome-terminal instead of urxvt (we
haven't installed that yet). Alternatively, you could install urxvt and use
that if you're feeling adventurous. We'll also install dmenu.

    sudo yum install dmenu

[2]: http://en.wikipedia.org/wiki/Run_commands

Last setup step before we get on with the actual customization bit.  We want to
add bspwm to our xsessions so that when we log in, we get a nice little dropdown
that lets us choose to log in to either gnome-session or bspwm. gnome-session is
the desktop manager that Fedora is currently using (assuming you went with the
default install). This way, if we screw anything up in bspwm, we can log in to
gnome and still have it work.

    cp bspwm/contrib/lightdm/bspwm.desktop /usr/share/xsessions/bspwm.desktop
    cp bspwm/contrib/lightdm/bspwm-session /usr/bin/bspwm-session

Restart the system (top right menu or sudo shutdown now in a terminal). We
should see a convenient dropdown menu marked by a gear symbol when we log in
that lets us select bspwm as our desktop session!

Logging into bspwm for the first time is a rather panic-inducing experience.
Nothing happened! It looks like the wallpaper went blank, and that's it!
Luckily, we've installed sxhkd which gives us some nice, customizable shortcuts
for using bspwm. Open up a terminal with Super+Return (Super is the Windows key
- whatever's between Alt and Ctrl).  Yeah, the font is tiny. And there's no
wallpaper on this desktop! And the old screen gets stuck when I close the
terminal and stays that way until I open a new one! The font size is tiny.
bspwm is scary. Help, Jeff, bspwm is scary.

Yeah, bspwm is mainly concerned with arranging your windows in a pretty way.
The other stuff like desktop wallpapers, screen scaling, and whatnot is up to
you. Here's what we're going to do:

 1. Set up xrandr to fix our screen resolution
 2. Install and configure feh for managing wallpapers


xrandr
------
Most of the following section is from the [ArchWiki article on xrandr][3]. They
are reproduced here because things on the internet tend to go away sometimes.

[3]: https://wiki.archlinux.org/index.php/xrandr#Adding_undetected_resolutions

Generate a modeline:

    cvt 1920 1080

Add the generated modeline to xrandr (don't copy mine verbatim!)

    xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync

You'll have to figure out which display to add it to. Running xrandr might clue
you in to this. For me, it's eDP1.

    xrandr --addmode eDP1 "1920x1080_60.00"

We'll set it as the current output mode

    xrandr --output eDP1 --mode "1920x1080_60.00"

Finally, to make this persistent, we'll drop this in our session file. Edit
/usr/bin/bspwm-session and add the xrandr lines to it, just above where sxhkd
and bspwm are started. Here's an excerpt from mine:

    xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
    xrandr --addmode eDP1 "1920x1080_60.00"
    xrandr --output eDP1 --mode "1920x1080_60.00"
    
    # Launch sxhkd:
    sxhkd &
    
    # Launch bspwm:
    bspwm

feh
---
feh is an image viewer that we'll be using as our desktop background manager. I
like to just drop all of my wallpapers in one directory and let feh randomly
cycle through them for me.

You know the drill by now:

    sudo yum install feh

And, as always, [ArchWiki has a good article on feh][4]. How you organize your
wallpapers is up to you, but here's how I like to organize them:

[4]: https://wiki.archlinux.org/index.php/Feh

    cd ~
    mkdir -p customization/wallpapers

Download some wallpapers to your wallpaper directory and

    feh --randomize --bg-fill ~/customization/wallpapers/*

Again, in order to run this at every login, we'll want to drop that last line
into /usr/bin/bspwm-session, between the xrandr lines and sxhkd. Now go forth
and download wallpapers to your heart's content. Some websites that I browse:

 * [Desktopography](http://desktopography.net/)
 * [Wallbase](http://wallbase.cc/)
 * [Konachan](http://konachan.net)
 * [/r/wallpapers](http://www.reddit.com/r/wallpapers)
 * [/r/wallpaper](http://www.reddit.com/r/wallpaper)
 * [/r/wallpaperdump](http://www.reddit.com/r/wallpaperdump)
 * [/r/animewallpaper](http://www.reddit.com/r/Animewallpaper/)
 * [/w/](http://4chan.org/w/)
 * [/wg/](http://4chan.org/wg/)

I also curate a [wallpaper blog][5] (kinda dead at the moment, with plans for
revival).

[5]: http://somanywallpapers.tumblr.com/

I think we'll wrap it up here. Next time we get into the fun customization bits!

