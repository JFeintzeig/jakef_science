---
title: "Microcorruption Mania"
date: 2024-04-28T17:53:43-04:00
---

At first, Microcorruption was just a diversion. Whenever I was bored with my main project, I could spend a few hours on a fun puzzle, learn about buffer overflows, and get a nice hit of endorphins when I solved it. But as the levels became more involved I got sucked in deeper. I would start first thing in the morning, racing against the clock because I wouldn't be able to fall sleep until I solved it. I found myself hunched over my laptop late at night, writing scripts to calculate hash collisions or reading long blog posts on heap exploit techniques. Or worse, laying in bed with my eyes closed but feeling wide awake as I visualized hex dumps and stack pointers. I'd developed *Microcorruption Mania*.

[Microcorruption](https://microcorruption.com/) is a capture-the-flag (CTF) challenge focused on binary reverse engineering and exploitation. You're given a digital lock, a disassembly of the firmware, and a debugger, and the goal is to unlock it. It consists of 25 levels that serve as a tour of the last 20 years of exploit development: stack buffer overflows, heap exploits, ASLR bypasses, ROP chains. I finished every level but it required ~150 hours of work, some late nights, and a whole lot of elbow grease.

This post is split into 2 sections: the first covers high-level lessons I found interesting or surprising as a newbie to reversing and exploits; I'll discusses general techniques but not level-specific spoilers. The second part dives into the details of how I applied these lessons in two levels: Chernobyl and Hollywood. This second part has spoilers!

### Lessons

**Fuzzing for the win:** At first I approached a new level by reading the disassembly to look for vulnerabilities. This was time-consuming and ineffective: most vulnerabilities were hard to spot given my lack of experience. They were subtle and require understanding the code at a deeper level than I could from just reading. It was instead ~100x easier to do "fuzzing": manually constructing semi-random input to test the hard edges of the program's logic. I often started by entering a long list of the character "a" to test the bounds of any buffers and look for overflows.

This isn't to say that reading the disassembly was a waste of time&mdash;finding a vulnerability often required both. After fuzzing for a bit and finding nothing, I would then read the code to understand the logic better and adjust my fuzzing.

A positive outcome from fuzzing is a program crash, usually an error message in the console. I knew I was onto something if I ever saw `insn address unaligned` or an error message containing `6161`. The letter `a` is `0x61` in hex, so such errors were caused by the program interpreting my user input as a memory address. This represented a capital-P Problem: getting input data interpreted as code was the start of most exploits[^0].

**Debuggers can do amazing things**: I never truly appreciated the power of a debugger until spending hours stepping through microcorruption. As a developer I used debuggers to zero-in on a known problem and inspect the state of my program's variables at that time. With access to the source code, I usually operate at the abstraction layer of the *business logic*. But debuggers also provide a window into the nitty-gritty details of a processor: the values in registers, the state of memory, and the results of an individual instruction. If you're developing exploits or doing performance engineering, this information is invaluable.

Similarly, I'd underappreciated how a debugger can be used not only to inspect this internal state but to change it: overwriting the values in a register, even changing the machine code being executed. During some levels I redirected the program counter (PC) to an unused function to see what it did, or to test later stages of an exploit before figuring out the early stages. In one level manipulating the program state made the problem go from seemingly intractable to straightforward (more below). 

**Breaking Abstraction Layers:** If I had to sum up my current understanding of exploit development, I'd say it's about breaking abstraction layers. A program has routines that achieve a certain purpose, like "set a variable to a value" or "check how a variable compares with an expectation". One layer deeper, those routines often get implemented by allocating memory or loading a pointer and checking its value. And one layer deeper than that, those implementations often break down to something simple like "change this byte at this location in memory to this value" or "update this register in this way". An exploit often requires thinking in these latter layers: which byte in memory is critical? How do I need to change it? Vulnerable programs provide tools to do these modifications, but identifying and using the tools often require thinking in the layers below the business logic.

**Creativity:** Working on microcorruption forced me to reconsider how I approach problems. In my career I've usually approached problems top-down: form a plan, break the problem into smaller pieces, repeat until each individual piece is tractable. A reductionist approach.

But in exploit development this often falls short. I can come up with a top-down plan: to get from A to Z you need to do B, C, D. But sometimes after spending hours solving the first two pieces, I find the remaining piece is literally impossible, making the entire plan impossible. I often become fixated on that last piece (surely there's just something I'm missing!), try to solve it over and over without making progress, and get frustrated. My pre-conceived plan leads to tunnel vision and hinders my progress.

So then I can either give up, or I can zoom out and look elsewhere: explore areas of the program I haven't yet. During one level, after getting stuck I wrote down 3 new approaches to the problem and researched each of them: all proved to be dead ends. Then I looked at a piece of code I hadn't before and noticed some surprising behavior. It led me to an approach I hadn't imagined, which ultimately worked. It felt like I was actually thinking creatively.

What does it mean to think creatively? I don't really know but this felt close because it involved *creating* a path. Instead of reducing a big problem to smaller ones, microcorruption required me to first cast a wide net, take stock of the available tools, and figure out how to combine them to achieve the goal. It's problem solving but it's more bottoms-up, opportunistic, and spontaneous. It's been challenging to switch to this mode of thinking, but I'm slowly getting better at it.

### Level Write-ups

This is not comprehensive, just a dive into some aspects I enjoyed. Spoilers below, beware!

#### Chernobyl

Upon opening Chernobyl, the first thing I noticed was the sheer *size* of the disassembly. This level was much larger and more complex than the previous ones. I settled in for a long haul.

I got a solid foothold by following the first two techniques I outlined above. By reading the disassembly and stepping through the debugger I learned the program creates a hashmap of usernames and passwords that's allocated dynamically on the heap, and I discovered how to add new entries to the hashmap. Fuzzing helped me find a memory corruption vulnerability: multiple inputs with the same hash overflowed a buffer on the heap, causing the program to crash. To figure out the exact cause of the crash, I used the debugger to call an unused function that printed the heap status to the console. This showed the heap was implemented as a doubly-linked list: an area of memory was split into chunks; each chunk had a header with its size, status, and pointers to the previous/next chunks; the buffer overflow overwrote headers for two heap chunks. Now I just needed to turn this vulnerability into an exploit.

This is where I got stuck, and spent many days wandering in the dark before the techniques above helped me find the solution. Because the program crash came from the `malloc` function, I first tried to build an exploit using `malloc` . Over a few days I developed a series of increasingly wacky and convoluted approaches, each of which failed:

* Tricked `malloc` into creating a use-after-free scenario, but it was not exploitable.
* Used `malloc` to wrap the address space and write values to low memory addresses that were used in other parts of the program, but developing a complete, successful exploit required a level of complexity and precision that was unmanageable.
* Tricked `malloc` into allocating overlapping chunks of memory from two separate calls. This almost worked but the memory footrprint was off by just one or two bytes from what I needed.

At this point I was stuck and frustrated and exhausted (remember, Microcorruption *Mania*). So I pivoted and tried to think more creatively. I'd developed tunnel vision by focusing only on `malloc` and ignoring most other code. A call to `free` also made use of heap chunk headers, so I examined that and quickly found the missing piece: it's internal logic had fewer constraints, giving me write access to more sensitive memory areas.

At this point solving the level should've been straightforward, but I'd been so focused on `malloc` that it biased my thinking. I failed to cut through the abstraction layers: I saw `free` only as a way to modify specific pieces of the hashmap, when in reality it was an unconstrained write primitive that could directly modify the most sensitive pieces of memory.

I still managed to solve the level *my way*, and using creativity to re-assess the tools I had available was critical for getting unstuck. But my solution required a few unnecessary steps and a lot of extra hash collisions and dealing with some frustration, while a simpler and easier approach would've sufficed.

I learned the lessons, but it felt like I learned them the hard way!

#### Hollywood

This level was challenging because the binary was obfuscated and packed, but I was able to use some creative debugger techniques to make the most challenging aspects tractable.

The code implemented obfuscation in a few different ways. The first used dummy jump instructions to trick the disassembler into misinterpreting the machine code; I fixed this with some regex's. The next challenge was randomization: the routine picked a random location in memory, copied code there and deleted the original, and then executed the copied version and repeated itself. This anti-debug logic made understanding program flow nearly impossible, but luckily I could use the debugger to remove it: I overwrote the code to replace the randomization with no ops, making the main part of the code stay in one place without getting deleted.

This didn't solve all my problems because the binary was still packed: the disassembly was a simple loop that decrypted data in memory and executed the result as code. Reading the packed code and follow the execution flow was still too tedious and confusing.

Once again the debugger provided a work around. Since I'd overwitten the randomization procedure, I knew exactly where the final, decrypted bytes were stored before being executed. So I set a breakpoint in the debugger when this occurred, ran the program, and manually copied the unpacked bytes from memory into a text file on each iteration (about 30 times). Disassembling this series of bytes revealed the actual unlocking mechanism.

It was partially obfuscated but surprisingly simple: it applied a custom hashing function to the password and compared it to a hardcoded value. I manually overwrote some registers to confirm the lock would indeed unlock given these hardcoded values. Now I just need to crack it.

At first I tried to "do the math" but realized the function might not be reversible. It looked like only a handful of input bytes would be required to get the correct result, so I recreated the hashing routine in Go and brute-forced it. It took less than a minute (~500M iterations) to find a five-byte sequence that satisfied the logic and solved the level.

### Teach Me The Ways

Do you have experience with RE and exploit development? What approaches have you found valuable? How do your strategies differ when doing modern vulnerability research? If you have thoughts, I'd love to hear from you.

[^0]: This also helped me understand why sanitizing error messages is important: attackers need feedback. While error messages are helpful to developers, they can also be used maliciously. If the microcorruption interface somehow caught these exceptions and didn't reveal the crash reason, I'd have had a much harder time. This is seen elsewhere too: for example, in cryptopals, the [CBC Padding Oracle](https://cryptopals.com/sets/3/challenges/17) shows how a seemingly-benign error message leads to big problems.
