---
layout: post
categories: customization linux
---

Every now and then someone in a class or hackathon will glance at my laptop
and ask me "woah, how did you get it to look like that?" I must confess that
desktop customization somehow became a hobby of mine some time in middle school
and has since devolved into something of a mild obsession. I tend to tinker
with my desktop environments a lot, both for aesthetic and practical purpose.

Some things before we start on this journey:
 * I will write all of these posts assuming you're running Fedora 20
 * I'm working on a Lenovo Ideapad Yoga 2 Pro
 * There are little tidbits of information about how Linux systems operate.
   This isn't strictly necessary if your goal is to just get a pretty desktop,
   but it'll probably be at least a little enriching to learn more about how
   your operating system works.

Now with that out of the way, let's begin.

Installing Fedora 20 on an external drive
-----------------------------------------
Before we start the actual installation bit, it's probably worth taking
some time to back up whatever's on your external drive. It's probably going
away soon. You'll also want to figure out the model number of the disk
you're installing to. You can find that in Disk Management in the Control Panel
on Windows. Right click your external, go to properties, and the name at the
top is your model number. Write that down. It'll be important soon.

Go ahead and download the
[Fedora 20 Live ISO](http://fedoraproject.org/en_GB/get-fedora "Fedora 20 Live ISO Download")
and put it on some installable medium. Personally I prefer to use the
[Live USB Creator](https://fedorahosted.org/liveusb-creator/ "Live USB Creator Download").
If you hit an issue with Live USB Creator not recognizing your flash drive,
you might have to open up a command line in the directory where Live USB
Creator is located and run

    liveusb-creator.exe --force F:\

Substitute E:\ for whichever drive letter corresponds to your flash drive as
necessary.

Next we're going to want to install Fedora 20 on the external drive. Plug in
your flash drive with Fedora 20 Live on it as well as your external hard drive
that you plan on installing on. Reboot your computer into Fedora 20 Live
(on the Yoga 2 Pro and similar Lenovo laptops, you'll have to press the special
little button next to the power button that brings up the BIOS boot menu).

When Fedora boots you should be greeted with a desktop and a window asking if
you want to install to your hard disk. Go ahead and click that now to start up
Anaconda, Fedora's installation program. Click through the language prompt to
be greeted with the following screen:

<img src="/images/blog/customization/install_summary.png" class="full-width" alt="Installation Summary">

Most of that is pretty self explanatory, except the installation destination
part. Click on that.

<img src="/images/blog/customization/install_destination.png" class="full-width" alt="Installation Destination">

This is where it gets a little tricky. I've only got one disk in that
screenshot there, but you probably have more. You'll need to figure out which
one is the one you want to install to. Remember that disk serial you wrote down
earlier? Hope you've still got it. It should correspond to a disk on the list.
Be *careful* about this. We're going to be formatting the disk so it's pretty
crucial you get this right as to not nuke your Windows installation or some
other hapless data you've got.

I assume you probably don't want to devote this entire external disk to Linux,
so we'll want to partition your disk so that part of it can be used as an
OS-neutral data partition for storing things you like carrying around.

<img src="/images/blog/customization/install_partition_1.png" class="full-width" alt="Partition Format">

Click "Done" and select "I want to review/modify my disk partitions before
continuing." Also, it should be the default, but if it isn't, you'll probably
want your partition scheme to be LVM. You can skip this step and choose
automatic partitioning if you don't want to have a separate data partition.

<img src="/images/blog/customization/install_partition_2.png" class="full-width" alt="Manual Partitioning">

Click on "Click here to create them automatically" to have Anaconda
make all of the important system mount points for you. If you want to use this
disk for data as well, modify the sizes of / and /home (if /home doesn't exist,
create it by clicking on the + sign at the bottom and choosing /home as the
mount point). / is your root directory -- the whole operating system uses / as
the base directory. /home is where user "home" directories get stored. These
directories contain all of the documents, media files, code, etc. that belong
to you (as opposed to things that belong to the system). With these things in
mind, choose sizes such that you have ample room in / and /home, but also leave
room for unpartitioned space for data. You can come back to this unpartitioned
space later with Windows Disk Management to allocate it for data as an NTFS
partition, and then you'll have a data partition that should work while you're
in either operating system. When you're done with this, click Done and
Accept Changes.

Now that that part's done, the rest of the install is fairly easy. Click Begin
Install and the installer will start to do its thing, writing the OS to your
hard disk. While it's doing that, you get to take care of passwords. Set a root
password and make your user account. Don't bother with the advanced user
creation settings, but you shoudl check off "Make this user administrator",
which lets you use sudo (I'll explain that later).

After the installer finishes, reboot your system into your new install
(remember you may have to go into the BIOS's boot menu to do that) and
boot into Fedora with the user account you just made.

Side note for the Yoga 2 Pro
----------------------------
I'm actually not 100% sure if this applies to just the Yoga or to all Ideapads
(I'm pretty sure it's the latter), but the first issue I hit on a fresh install
of Fedora 20 is that wifi doesn't work right out of the box. Some googling
reveals that the issue is a certain kernel module ideapad\_laptop that's bugging
out and preventing wifi from working. We can fix that. Open up a terminal,

    sudo rmmod ideapad_laptop

Let's break that down a bit.

"sudo" is short for "super user do". "Super user" refers to the root user. The
long and short of it is that Linux has a system of permissions that restrict
what a particular user can do, for security reasons. Here, sudo is modifying
the "rmmod" program, which means that it is executed with root privilege. We
have to do this because rmmod is touching important parts of the operating
system (the kernel) and it would be unwise to let just any user mess around
with the most fundamental part of your operating system. The root user is an
unrestricted user, which means it can do *anything*. This is very powerful and
also very dangerous! Be careful with root privilege!

rmmod means "remove module". A "module" refers to a loadable kernel module.
This probably isn't particularly pertinent information to you. Basically,
things such as device drivers are implemented in the kernel as modules so
that they can be enabled or disabled at will while the system is executing
and without having to reboot the system to take effect.

We're not quite done yet. LKMs are loaded at every boot, so if we don't make
Fedora stop trying to automatically load that module, we'll have no wifi every
time we reboot and have to run that command again. We want to blacklist the 
ideapad\_laptop module. In a terminal,

	cd /etc/modprobe.d
	su -c "echo blacklist ideapad_laptop > ideapad_laptop.conf"

cd means "change directory". In this case, it moved us to /etc/modprobe.d,
a directory where configuration files for modprobe, which is a program for
adding and removing kernel modules. We could've used modprobe -r instead of
rmmod. In fact, both modprobe and rmmod (among other programs) are actually
just linked to kmod, which is the program that manages loading/unloading
kernel modules. Changing directories allows us to run programs against the
programs in that directory. Of course, we could always type out the full paths
of the files, but that tends to get tedious.

su stands for "substitute user" and lets you become other users. That probably
sounds a little weird. How could you become another user? Well, as you can
probably imagine, it's another thing that requires root privilege to do
(can't have people going around impersonating each other, now). How this
actually functions is that it opens up a shell (the shell is your program that
interprets things that you type in and uses them to perform operations and
print output). This is distinct from your terminal, which is the graphical
frontend for your shell.

su -c specifies su with the option -c. There's really no difference between
the textual argument ("echo blacklist ...") and -c, but the - denotes that this
is an option. It's a convention present in all command-line applications that
I'm aware of. Typically a single hyphen for single-letter options and double
hyphens for longer. Often there are equivalents between these one letter
shorthands and the lengthier options (e.g. for su, -c and --command=COMMAND
mean the same thing). So su -c runs the command that comes after it as root.
Here, we're running echo. We had to do this as root because
/etc/modprobe.d/ is a system directory.

It's probably helpful at this point to discuss a little bit about what standard
input and standard output are. These two (along with stderr, standard error)
make up the "Standard streams" - input and output streams that are connected by
default for us so that we may interact with programs.

echo, as its name would imply, takes a textual parameter from the command line 
and just *echoes* it to output. In this case, our input is
"blacklist ideapad_laptop". echo takes that text and returns it to standard
output.

The > following "blacklist ideapad\_laptop" is telling our shell to redirect
standard output of echo to a file." > ideapad\_laptop.conf" writes stdout of
echo to a file in the current directory.

I think I'll wrap things up there for now. Next time, we get into the fun part:
customizing!