# crun - run docker images like regular applications
[ otherwise known as a horrible abuse of containers apparently ]

## thoughts
This is definitely not going to be completed anytime soon, but I wanted to
express my thoughts on the subject:

I love the concept of containers. They provide a level of security that Linux
has desperately needed, and also provide a measure of consistency between
distributions. Now, before anyone complains, I never said that they were the
end all be all.

I feel that Mac's application concept is still better than Windows, and Linux
needs a similar concept. The ability to do something like `crun "Skype.crate"`
would be immensely helpful.

Some of these thoughts, then, may not be correct. Feel free to voice your
thoughts.

### wayland is really really nice
The wayland protocol was created to replace the X server protocol, which is
still notorious for being completely unsecure. Exposing the X server socket to
a docker container is a horrendous idea, because any (and all) applications
have raw access to the screen, keyboard, etc. (It never isolated applications.)

Wayland DOES isolate applications, though it does seem to lack IPC at the
moment, which... I don't know what's going on there. But it's still safer.

All applications should be Wayland clients. Don't use Wayland? Thankfully,
we have XWayland.

### controlling what accesses what
One of the major flaws with Android (where'd that come from) is that users were
asked to approve permissions before installing the application. I believe that
is a huge mistake, as it leads to essentially people ignoring them because it's
the same shit different day. Linux doesn't even have these permissions, except
for standard `chmod`, `chown`, and possibly SELinux/AppArmor.

Well, we can change that by monitoring access to special devices. A horrible
way to do that would be to... _gulps nervously_ make a shared library with
open() functions to monitor what devices get called (soundcard, videocard, etc)
... and then prompt for permissions if we do that. But... yeah. That's hacky.

Another way is that we provide a socket and a library, and clients can call the
library to request permissions. We prompt, then bind mount /dev as needed (I'm
still debating how this'd work - we could bind mount all the ones on startup,
then bind mount them again in the container as permissions allow? hmmm.)

[ I like way #2 better just because I don't have to make a shared library that
would work across all images; instead, just make a go static binary to provide
this functionality ]

### being awesome in general
I think we can create a base image, which includes the core library and also a
static cli tool. Hopefully apps hook into the core library; otherwise, scripts
can call the cli tool so that apps don't break, etc.

## definitely not finalised yet
These are just my thoughts; I'm not sure how well it'd work out trying to
actually *do* it. Welp, time to try.

## license
bsd
