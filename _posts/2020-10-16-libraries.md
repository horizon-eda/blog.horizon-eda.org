---
layout: post
title:  "On Libraries"
date:   2020-10-16 0:1:0 +0100
categories: misc
author: Lukas
---

As with all nontrivial software projects these days, Horizon EDA is 
built on top a multitude of libraries and APIs. This post reflects on my 
experiences I've had interacting with them.

## Excellent

### SQLite

Despite having "Lite" in its name, I've never been let down by SQLite 
in terms of SQL features. Especially the support for recursive common 
table expression has proven invaluable for resolving dependencies 
among pool items. Its C API is straightforward to use without any bad 
surprises.

### Curl

Same as SQLite, curl falls into the "no one ever got fired for using 
it" category, so when the need came to do HTTP requests, I went with it and wrote a 
[simple C++ wrapper](https://github.com/horizon-eda/horizon/blob/master/src/util/http_client.cpp).
Curl's easy mode API actually lives up to its name and doesn't add any 
unexpected complexity.

### ZeroMQ

ZeroMQ is used in Horizon EDA for inter-process communication between 
the pool/project manager and the interactive manipulator (board, 
schmematic, etc. editor). Compared to TCP sockets, it's got the 
advantage of handling framing (start and end of messages) and 
messaging patterns such as request/response. Apart from the [odd bug 
where port number formatting depended on the 
locale](https://github.com/zeromq/libzmq/issues/3385) (that got 
promptly fixed) I have nothing negative to say about ZeroMQ as well and 
highly recommend it as an easy-to-use alternative to TCP sockets if 
both sides are to be implemented.

### JSON for Modern C++ 

This library is used throughout Horizon EDA for reading and writing 
JSON and it works so well that I almost forgot mentioning it here. If 
you need to interact with JSON in C++, look no further!

### Clipper

Without Clipper, Horizon EDA wouldn't have DRC, filled planes and 3D preview as it 
provides boolean and grow/shrink operations on polygons. I've had zero 
issues with it in terms of quality of the algorithms and its API is 
free from bad surprises.

### KiCad Router

Despite not being a library (or not intended as such) it's worth 
mentioning the KiCad router in this post, since without it, Horizon 
EDA wouldn't be what it is today. Getting it to compile outside of 
KiCad was surprisingly easy, so the [last slide of the FOSDEM talk on 
the 
router](https://archive.fosdem.org/2015/schedule/event/pcb_routing/attachments/slides/796/export/events/attachments/pcb_routing/slides/796/fosdem_router.pdf) held its promise!
There were some impedance mismatches down the road such the representation of 
pads, but overall the effort it took to integrate the 
router paid off very well.

## Good

### libgit2

For the "Remote" tab in the pool manager, I needed a way to do git 
operations from C++. I quickly ruled out shelling out the `git` 
command-line tool as that'd require parsing its sometimes-changing 
output and spawning a new process for each operation can be slow. Since 
the canonical library for building git-enabled applications is 
[libgit2](https://libgit2.org/), I set out to implement the desired 
features using, only to discover that libgit2 mostly consists of the 
so-called low-level plumbing commands, so creating a commit is 
multi-step process that didn't make much sense until I understood git's 
inner workings. On the upside, I now know a lot more about git than 
before. If it had and easy mode like curl, it'd have made it 
into the Excellent category.

### Gtkmm/Glibmm/Giomm (C++ bindings to the GNOME platform)

All in all, Gtkmm and the like do a really good job at mapping GNOME's 
GObject-based APIs to C++ in a way that works well with modern C++ 
concepts. Glib itself also made running Horizon EDA on Windows much 
easier as it provides a reasonable abstraction for OS-specific APIs 
such as file operations and process management.

### Gtk

Due to it's central role as the UI toolkit, Gtk deserves its own 
section. As with any UI toolkit, the application is
married to or held hostage by it as it's very costly in terms of 
knowledge and time to switch to another one. After having worked with 
Gtk for many years, using it has become almost second nature since over 
time my mental model of how UIs are made aligned with Gtk's. Alluding 
to the hostage metaphor, this could be described as Stockholm syndrome 
as I grew accustomed to some of Gtk's oddities.

Going back to technicalities, here's what I like about Gtk/Gtkmm (take this 
with a grain of salt due to Stockholm Syndrome):

 - Straightforward API 
 - Separation of UI design from code with Glade
 - Modern UI patterns that don't look like Windows 95 with a different 
   shade of grey
 - Opentype features support
 - Support for modern input devices such as touchscreens and 
   pixel-level scrolling

Even though my significant investment in Gtk and the surrounding 
libraries, there are couple of things in Gtk I'm not happy about:

 - Several issues surrounding GtkGLArea
   - Occasional corruption of window contents on X11
   - Odd bugs such as the GtkGLArea updating a frame too late on Windows 
    with Intel drivers
   - Some AMD GPUs appear not to work at all on Windows, though this 
    might be due to buggy drivers
 - UI elements such as buttons could be a bit smaller to allow for 
   higher content density
 - Glade doesn't see much love, there only appears to be one active 
   developer
 - Typing in the filechooser triggers search rather than prefix 
   matching
 - Windows being dimmed if unfocused. While this may fit in well with 
   the GNOME desktop, it's especially jarring with tiling window 
   managers.
 - [Gtk 4 dropping GtkMenu without an adequate 
   replacement](https://discourse.gnome.org/t/using-gtkpopovermenu-as-a-gtkmenu-replacement/3786/22)

Nevertheless, I still recommend Gtk as a great library to develop 
applications for the Linux desktop and with some limitations as a 
cross-platform application toolkit.

### OpenGL

OpenGL sits in a similar spot as Gtk since it's unlikely that Horizon EDA 
will ever move away from it. After having moved past the initial 
boilerplate for setting up shaders and buffers it's API is fairly easy 
to use for 2D and simple 3D rendering. There might be other nicer APIs, 
but since my understanding of 3D graphics got shaped by OpenGL, it's my 
local optimum for now.

## Ugly

### Open CASCADE

When it comes, to importing and exporting 3D models in the STEP format, 
Open CASCADE is the only viable open source option out there. Since the 
library isn't exactly easy to use, all of the Open CASCADE-related code in 
Horizon EDA is based on the corresponding code in KiCad. Looking closer 
the KiCad it reveals that all(?) of it was written by [one 
guy](https://gitlab.com/users/cbernardo), so there doesn't seem a lot 
of knowledge about Open CASCADE out there.
