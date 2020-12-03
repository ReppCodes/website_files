---
title: GIOS Midterm Practice Questions
published: true
---

***
### Part 1
1. What are the key roles of an operating system?
2. Can you make distinction between OS abstractions, mechanisms, policies
3. What does the principle of separation of mechanism and policy mean?
4. What does the principle optimize for the common case mean?
5. What happens during a user-kernel mode crossing?
6. What are some of the reasons why user-kernel mode crossing happens?
7. What is a kernel trap? Why does it happen? What are the steps that take place during a kernel trap?
8. What is a system call? How does it happen? What are the steps that take place during a system call?
9. Contrast the design decisions and performance tradeoffs among monolithic, modular and microkernel-based OS designs.

### Part 2
10. Process vs. thread, describe the distinctions. What happens on a process vs. thread context switch.
11. Describe the states in a lifetime of a process?
12. Describe the lifetime of a thread?
13. Describe all the steps which take place for a process to transition form a waiting (blocked) state to a running (executing on the CPU) state.
14. What are the pros-and-cons of message-based vs. shared-memory-based IPC.
15. What are benefits of multithreading? When is it useful to add more threads, when does adding threads lead to pure overhead? What are the possible sources of overhead associated with multithreading?
16. Describe the boss-worked multithreading pattern. If you need to improve a performance metric like throughput or response time, what could you do in a boss-worker model? What are the limiting factors in improving performance with this pattern?
17. Describe the pipelined multithreading pattern. If you need to improve a performance metric like throughput or response time, what could you do in a pipelined model? What are the limiting factors in improving performance with this pattern?
18. What are mutexes? What are condition variables? Can you quickly write the steps/code for entering/existing a critical section for problems such as reader/writer, reader/writer with selective priority (e.g., reader priority vs. writer priority)? What are spurious wake-ups, how do you avoid them, and can you always avoid them? Do you understand the need for using a while() look for the predicate check in the critical section entry code examples in the lessons?
19. What’s a simple way to prevent deadlocks? Why?
20. Can you explain the relationship among kernel vs. user-level threads? Think though a general mxn scenario (as described in the Solaris papers), and in the current Linux model. What happens during scheduling, synchronization and signaling in these cases?
21. Can you explain why some of the mechanisms described in the Solaris papers (for configuring the degree concurrency, for signaling, the use of LWP…) are not used or necessary in the current threads model in Linux?
22. What’s an interrupt? What’s a signal? What happens during interrupt or signal handling? How does the OS know what to execute in response to a interrupt or signal? Can each process configure their own signal handler? Can each thread have their own signal handler?
23. What’s the potential issue if a interrupt or signal handler needs to lock a mutex? What’s the workaround described in the Solaris papers?
24. Contrast the pros-and-cons of a multithreaded (MT) and multiprocess (MP) implementation of a webserver, as described in the Flash paper.
25. What are the benefits of the event-based model described in the Flash paper over MT and MP? What are the limitations? Would you convert the AMPED model into a AMTED (async multi-threaded event-driven)? How do you think ab AMTED version of Flash would compare to the AMPED version of Flash?
26. There are several sets of experimental results from the Flash paper discussed in the lesson. Do you understand the purpose of each set of experiments (what was the question they wanted to answer)? Do you understand why the experiment was structured in a particular why (why they chose the variables to be varied, the workload parameters, the measured metric…).
27. If you ran your server from the class project for two different traces: (i) many requests for a single file, and (ii) many random requests across a very large pool of very large files, what do you think would happen as you add more threads to your server? Can you sketch a hypothetical graph?