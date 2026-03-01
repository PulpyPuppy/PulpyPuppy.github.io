---
params:
  author: Temirlan Emilbekov a.k.a. PulpyPuppy
title: The First Page
date: 2026-02-26
description: What I have done so far
type: post
---

Hi! I would like to tell about and structure everything I’ve done so far working on porting of PortableGL to RTEMS. It would be so beautiful if I could write in Russian. I’d show all my writing abilities and potential 🥀. But English is nice as well.

To be honest, I was a little bit worried about entering the Discourse etc. But, thanks the Universe, I do this all my life — write to all sorts of strange people in strange places.

After “saying hello” on Discourse and browsing topics, I’ve stopped on guess what. I’ve started trying to run PortableGL test app on RTEMS after getting a little roadmap from Gedare. I’ve met some bunch of problems while doing it. They were mostly about QEMU emulator graphics-related flags. The last issue was a little one. It was about the color scheme. After fixing it, all ran.

After running and reporting this stuff, I started doing some research.  I’ve got some questions for this research, one of them was “How would I pass the right channels order”, cause the PortableGL’s set was kinda limited. I’ve taken a look on `fb.h`, `framebuffer.h` and `portablegl.h`. It can be done by setting all the color-channel-related macros. I’ve found [some bug](https://github.com/rswinkle/PortableGL/commit/a7ca435a563ee4195a4d8d72b113cc6c8133aceb), which fix was taken to the upstream ❤️ (not as PR though ❤‍🩹).

There is also a typo in README: `prexix` 🥀. PR this later.

*It must some kind of curse, I'm constantly writing pROtablegl*