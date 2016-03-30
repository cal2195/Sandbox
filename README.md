# Sandbox

A little shell script to create various types of sandboxes on the fly!

## Prerequisites
* Linux - *(xUbuntu and openSUSE tested)*
* /bin/bash *(Others are untested!)*
* unionfs-fuse - (installed automatically using the **install** command)

## What does it do?
Sandbox is a little shell script that mounts an union file system over your current root file system. You can think of it like placing a sheet of *transparent paper* over your current file system. All files can be read from within the sandbox, but any changes or **writes get written to the paper**, not the original file system!

In this way, if you decide you want to undo what you've just done, you just remove the paper, **ie.** the sandbox.

There are quite a few **different types of sandboxes** available, so whether you need a quick ephemeral container, to a permanent sandbox to host your testing and development applications in, we've got you covered!

## Usage

### How do I install it?
Simply run `./sandbox install` - The **install** command will install all needed dependencies, and also add the executable to your path.

Alternatively, you can install `unionfs-fuse` yourself and use `./sandbox` as a portable application!

To get the latest version of the script, regardless how you installed it, you can use the **update** command.

`sandbox update`

*Please note this requires `curl` installed!*

### Okay, now what?
Once installed, you can create a sandbox from anywhere by typing `sandbox` in a terminal. This will create a **temporary** sandbox, that will be destroyed once you exit it. See *Types of Sandboxes* below!

### That's cool! But what if I want to access the sandbox again?
To prevent the sandbox from being destroyed, you can give it a **name**!

`sandbox -n firefox`

This will create a **semi-permanent** sandbox, which will persist until *reboot*. (**see warnings below!**)

Once you name the sandbox, you can connect multiple terminals to the same sandbox by using the **same name**. You can also reattach to an exited sandbox by using it's name.

### Nice! But what if I want a sandbox to persist between reboots?
To make a sandbox **permanent**, you just add the `-p` flag!

`sandbox -n dontdeleteme -p` or more simply `sandbox -pn dontdeleteme`

Keep in mind this flag requires a name, otherwise you won't be able to get back to it!

### Uh oh! I've forgotten what I named my sandbox!
Don't fear my friend, we've got you covered! To list all **semi-permanent** sandboxes, just use the list flag!

`sandbox -l`

If it's a **permanent** sandbox you're after, simply add the permanent flag!

`sandbox -p -l` or `sandbox -pl`

### Is it only the terminal that's sandboxed?
Anything that's done from within that terminal is also sandboxed! So if you run `firefox` from within a sandbox, it will also be part of the sandbox! You can also run your file manager or text editor to view the files within the sandbox!

### Am I able to sandbox an application without using the shell?
Yes you can! Just add the command for the application after the flags!

`sandbox -pn webdev firefox` - Will immediately run **firefox** in a sandbox called *webdev*!

If you need to **pass in parameters**, make sure you enclose them in quotes!

`sandbox -pn stay "mousepad /home/cal/Sandbox/README.md"`

### Awesome! It's annoying having to install all my software everytime though... Can you help?
No problem! You need *sand grains*! These are little pre-configured scripts for automatically installing software in your sandboxes! Simply use the `-g` flag, comma separating any sand grains you would like to install!

`sandbox -g firefox,transmission -n downloadbox`

Want to know what sand grains are available? Simply use the `-g list` option!

`sandbox -g list`

Your piece of software not there? Why not make one! Take a look at the `sandgrains` folder above to see how they're done, **fork this repo** and **make a pull request** once you're done! :D

### How can I get files out of the sandbox?
To access the filesystem of your sandbox, just use the `-o` flag!

`sandbox -pn mysandbox -o`

This will open a folder at the **root** of your sandbox, allowing you to locate your files within the sandbox, and copy them elsewhere if desired!

### Is it speed or security you're after?
Sandbox also supports **RAM Disks**, which being backed by your RAM has some interesting pros!
#### Pros
* Speed! - RAM is much **faster** than hard disks, so you'll get much faster read write speeds!

* Security! - As RAM is wiped on reboot, all changes will be *forever* lost to the void! As long as you keep in mind the warnings, it's a much more **secure way** to store temporary data as nothing will be written to disk! (**See warnings!**)

#### Cons
* Limited Space! - As a RAM disk is backed by your available RAM, **you'll only be able to write as much data as you have free memory (RAM) left!** So a 1GB file would need at least 1GB free RAM to store it! Keep this in mind as running out of RAM will not only produce **write errors** in your Sandbox, but you also risk **crashing your host system** if you run out of free memory! D:

* Swap Leaks! - In contrast to the **Security** pro above, if you have swap enabled on your system, you will negate the security factor! As RAM fills up, Linux will put certain portions of the RAM onto your swap partition, writing it to disk! Unless this swap is encrypted, you'll be writing your sandbox directly to disk, making it possible to recover the data with special tools!

Now! After you've read through all that, to spawn a RAM backed sandbox, simple add the memory flag!

`sandbox -m` or `sandbox -m -n coolramdisk`

There's no point using the permanent flag with this one, as RAM is wiped once it looses power!

### Can you compare all the types of sandboxes in a nice table for me?
Sure can do!

Type of Table | Lifespan | Command
--------------|----------|--------
Temporary | Until you exit the sandbox | `sandbox`
Semi-permanent | Until reboot (see warnings) | `sandbox -n prettyname`
Permanent | Until you delete it with the `-d` flag | `sandbox -pn staywithme`
Temporary RAM | Until reboot | `sandbox -m`


### So much information! How will I remember it all? D:
To get a quick look at all the commands available you can use the help flag!
`sandbox -h`
It will produce this output, which should be enough to jog your memory!
```
Usage: ./sandbox [OPTIONS]... [COMMAND]
    -n NAME     name the sandbox
    -d          delete the named sandbox
    -l          list all sandboxes
    -p          permanent sandbox
    -m          in-memory sandbox
    -g          comma separated grains, or 'list' to see all
    -o          opens your filemanager to see the sandbox's filesystem
    -h          show this help text
    COMMAND     command to run instead of shell

See https://github.com/cal2195/Sandbox for more information!
```

## Advanced Usage
### Nesting Sandboxes
Sandboxes can be happily nested, simply just call another `sandbox` command from within a sandbox! This would allow you to have multiple "roll back" points if you're trying out many new things!

## Warnings!
* If you're using the RAM disk option, **make sure** you read the warnings above in the memory section!
* It should also be noted that **semi-permanent** sandboxes hang around until the `/tmp` folder is wiped, which is generally configured for just after the **boot up** of the system!
