---
layout: page
title: CS6210 Advanced Operating Systems
---

For Spring 2021, my fifth semester, I chose to take CS6210: Advanced Operating Systems.  This class addresses a broad range of topics in operating system design and implementation, including:
* Operating system structuring
* Synchronization, communication and scheduling in parallel systems
* Distributed systems, their communication mechanisms, distributed objects and middleware
* Failures and recovery management
* System support for Internet-scale computing

I chose to take this class due to my ongoing interest in systems programming, low-level design, and optimization.  It also followed quite well on having taken the Intro to OS course immediately prior, which will (hopefully) help reduce some of the remedial catch-up work I frequently need to do given my non-traditional background in CS.

The theoretical material in the lectures and exams is augmented with rather large and complex projects in C and C++.  There are four of these projects:
1. Virtual Machine Scheduling in KVM
2. Barrier Synchronization
3. Distributed Service using GRPC
4. Implement MapReduce Framework using GRPC

I switched jobs towards the end of the semester, and between that and the final project being a monster my class notes are incomplete.  I've posted here what I managed to take, both for my own future reference and in case even partial notes might be helpful to someone.

***

* The screenshot images in these lecture notes are property of Georgia Tech.  You can find the originals in the publicly available [Kaltura videos](https://omscs.gatech.edu/cs-6210-advanced-operating-systems-course-videos).

<section>
<h3>Lecture Notes</h3>
<li>
<a href="{{ "/aos_lec_L01" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 1 - Introduction to AOS
</a>
</li>

<li>
<a href="{{ "/aos_lec_L02" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 2 - OS Structures
</a>
</li>

<li>
<a href="{{ "/aos_lec_L03" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 3 - Virtualization
</a>
</li>

<li>
<a href="{{ "/aos_lec_L04" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 4 - Sharing Semantics
</a>
</li>

<li>
<a href="{{ "/aos_lec_L05" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 5 - Distributed Systems
</a>
</li>

<li>
<a href="{{ "/aos_lec_L06" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 6 - Distributed Objects and Middleware
</a>
</li>

<li>
<a href="{{ "/aos_lec_L07" | prepend: site.baseurl | append: ".html" | replace: '//', '/' }}">
    Lesson 7 - Distributed Subsystems
</a>
</li>

</section>