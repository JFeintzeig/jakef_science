---
title: "A Recurse Center Return Statement"
date: 2024-04-11T15:22:32-04:00
---

I recent completed 18 weeks at the [Recurse Center](https://recurse.com/), "the retreat where curious programmers recharge and grow". It's customary to write a Return Statement[^0] reflecting on your experience. Here's mine.

My vague goal when I started RC was to learn "how computers work" at a basic level. I wanted to explore topics in security and low-level systems, and I wanted to stretch my abilities in thinking adversarially, being creative, and designing software. I wanted to use RC as an opportunity to go deep on topics I found interesting, and to reflect on my career so far and what I want to do next (data science management? security? a startup? something else?).

### What I Worked On

During the course of my batch, I worked on:

* A Gameboy emulator. I built this from scratch, in Go (also, I learned Go). It took me 8 weeks of solid work to get it functional enough to play Tetris. I still have some bugs that need fixing to play other games.
* Completed all of [microcorruption](https://microcorruption.com/), a capture-the-flag focused on binary reverse engineering + exploitation. It starts with basic disassembly and works up to ROP, heap corruption, obfuscation.
* Worked through the first 4 sets of [cryptopals](https://cryptopals.com/) (also in Go), a series of challenges in which you implement, and then attack, common cryptographic algorithms.
* Built a ray tracer in C (also learned C) by working through [this book](https://raytracing.github.io/books/RayTracingInOneWeekend.html), and then spent 6 weeks optimizing it. I got [into]({{< ref "/posts/gcc-wtf" >}}) [the]({{< ref "/posts/recursion-riddle" >}}) [weeds]({{< ref "/posts/simd-parallelism" >}}) of performance engineering and made my program ~500x faster.
* Smaller projects:
  - Completed the first half of [2023 Advent of Code](https://github.com/JFeintzeig/advent_of_code_2023) (in Go).
  * Started learning embedded development, did a few ESP32 tutorials (in C), gave up.
  * Researched polyglot files and created a `.pyc` / `.zip` polyglot.
  * Implemented a basic web server in C while learning Linux socket programming.
* Pair programmed: I pair programmed with 43 people over 60 sessions, on projects ranging from assembly calling conventions to CSS animations. We worked in 13 languages[^1] and I learned at least one thing every session. The best ones led to breakthroughs in understanding or inspiration that changed what I worked on.
* Interest groups:
  * I led a weekly interest group on Emulators and Virtual Machines that became a mainstay of my batch. We discussed everything from gaming emulators to Web Assembly, CPython, virtualization, JITs.
  * I attended weekly meet-ups for cryptopals and C programming, and ocassionally attended workshops on CTFs, authentication, quantum computing.
  * I started, and then quickly abandoned, an interest group on embedded systems.

Through all this my north star was "if I'm looking at hexdumps and disassembly, I'm doing the right thing", and this helped me navigate decently well.

### Learning about Learning

I learned more about what I find interesting. At the beginning I described my interests vaguely as "how computers work" and "getting under the hood". Now I'd say I'm interested in projects that break through abstraction layers to below the operating system, especially where knowledge of the hardware informs the software. Performance optimization, emulators, and reverse engineering all have this in common. This is helpful because it both focuses and expands career directions I'm curious about; some aspects of security fit this better than others, and some areas outside security fit this well too.

I also now have greater confidence in my ability to improve as a programmer. As a data scientist I've always been insecure about not being a "real software engineer". RC helped dispel this because I was surrounded by talented, experienced people, and they were all struggling with hard problems, and then succeeding, and then explaining their process so I could visualize how I could do it too. I always thought data structures and algorithms were "beyond me", but collaborating on Advent of Code showed me it's not magic[^2].

RC also reminded me how much I enjoy doing research. While working on Gameboy I relished the feeling of mastering a complex system: being so immersed in something intricate that you can hold it in your head and spin it around. While I love these types of projects, it didn't push my critical thinking or creativity to my limits. But working on microcorruption surely did, and so did optimizing my ray tracer. All three projects were equally new and involved acquiring knowledge. But the latter two required a different style of work. They required experimentation, creativity, navigating uncertainty, and breaking things down and making them anew. They required *discovering* a solution rather than *building* one. I like doing both, but I've had less opportunity for discovery recently and I've missed it. 

### How I Approached RC

How I approached to RC&mdash;and how my work made me feel&mdash;breaks up into three sections. I spent the first six weeks working on Gameboy without many distractions. At the beginning I felt a strong desire to *produce something*. I wanted to have something to show for my short time at RC, something impressive I could point to and say "I built that." During this time I generally felt a bit rushed and "behind schedule" (lol what schedule?) and a little overwhelmed.

In the second six weeks, even though Gameboy wasn't "done", I realized I wasn't taking advantage of RC as much as I could. My work was probably 80% solo. I was engaging with others but I'd skipped opportunities for group projects. So I joined a crew of people working on cryptopals, and I said "yes" to more meet-ups, and I tried to pair with 4 people every week. This was fun and added a dimension to my experience: working through cryptopals was a highlight both because of the relationships I built and how it accelerated my learning. But I also felt more scattered and a little insecure: most my projects looked like problem sets instead of building something new. When I chatted with old co-workers, I was self-conscious they'd think I was at summer camp instead of this intellectually rigorous experience (obviously not mutually exclusive!). My 12-week retreat was ending but I still had a list of project ideas and an open calendar, so I extended for another 6 weeks.

In the last six weeks I somehow overcame these worries about being "productive" or building something "impressive". Instead I tinkered, and paired, and followed my nose to whatever surprised me. This started with a side quest to make a ray tracer[^3]. When I finished it, I figured I'd spend a couple days making it faster before moving on. But the more I poked at it, the more questions I had. Instead of feeling rushed and anxious, I just took my time and poked more. Before I knew it six weeks had gone by and I was still optimizing the ray tracer, but three layers deeper in the stack than I'd started. I was still working towards my original goal, just in a different context than I'd imagined. And while the project started as a toy, by the end I was engaging with current research, and documentation intended for people who *write compilers*, and doing things that felt quite real, without ever intending to. And that was fun and rewarding.

If I was starting all over again, I think I would strive to have this approach (and its accompanying emotional state) sooner. I'd also engage more deeply with other people's projects. I was always very aware of what others were working on, but if it was something I didn't know much about I glossed over it. If I'd engaged more deeply, I probably would've developed more new interests.

### Why Do RC?

I really recommend RC! RC is unique because most people are motivated by their own interest. It's not about building something useful, or networking, or having something to put on a resume. One can also accomplish those things too, but the vibe at RC is focused on learning and growth: identifying where your abilities are today and where you want to be tomorrow, pushing your limits and following your curiosity, and sharing what you learn.

If this sounds like what you want to do, I'd strongly recommend applying!

[^0]: A wordplay on a function `return`, I think?
[^1]: Go, C, Python, Ruby, Java, Rust, Zig, Swift, Dart, assembly, Javascript, CSS, and SQL.
[^2]: It's just dynamic programming&mdash;all of it, actually, everything I've found confusing or gotten stumped by, it's probably dynamic programming.
[^3]: I chose this project serendipitously; someone else was doing it and it looked fun!
