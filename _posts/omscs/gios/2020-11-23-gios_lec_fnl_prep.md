---
title: GIOS Final Exam Practice Questions
published: true
---

***
## Final Exam Review Questions

### Disclaimer
These questions were provided by the course staff but with no guarantee of being exhaustive coverage of the material.

### P3L1
1. How does scheduling work? What are the basic steps and datastructures involved in scheduling a thread on the CPU?
2. What are the overheads associated with scheduling? Do you understand the tradeoffs associated with the frequency of preemption and scheduling/what types of workloads benefit from frequent vs. infrequent intervention of the scheduler (short vs. long timeslices)?
3. Can you work through a scenario describing some workload mix (few threads, their compute and I/O phases) and for a given scheduling discipline compute various metrics like average time to completion, system throughput, wait time of the tasks…
4. Do you understand the motivation behind the multi-level feedback queue, why different queues have different timeslices, how do threads move between these queues… Can you contrast this with the O(1) scheduler? Do you understand what were the problems with the O(1) scheduler which led to the CFS?
5. Thinking about Fedorova’s paper on scheduling for chip multi processors, what’s the goal of the scheduler she’s arguing for? What are some performance counters that can be useful in identifying the workload properties (compute vs. memory bound) and the ability of the scheduler to maximize the system throughput.

### P3L2
6. How does the OS map the memory allocated to a process to the underlying physical memory? What happens when a process tries to access a page not present in physical memory? What happens when a process tries to access a page that hasn’t been allocated to it? What happens when a process tries to modify a page that’s write protected/how does COW work?
7. How do we deal with the fact that processes address more memory than physically available? What’s demand paging? How does page replacement work?
8. How does address translation work? What’s the role of the TLB?
Do you understand the relationships between the size of an address, the size of the address space, the size of a page, the size of the page table...
9. Do you understand the benefits of hierarchical page tables? For a given address format, can you workout the sizes of the page table structures in different layers?

### P3L3
10. For processes to share memory, what does the OS need to do? Do they use the same virtual addresses to access the same memory?
11. For processes to communicate using a shared memory-based communication channel, do they still have to copy data from one location to another? What are the costs associated with copying vs. (re-/m)mapping? What are the tradeoffs between message-based vs. shared-memory-based communication?
12. What are different ways you can implement synchronization between different processes (think what kids of options you had in Project 3).

### P3L4
13. To implement a synchronization mechanism, at the lowest level you need to rely on a hardware atomic instruction. Why? What are some examples?
14. Why are spinlocks useful? Would you use a spinlock in every place where you’re currently using a mutex?
15. Do you understand why is it useful to have more powerful synchronization constructs, like reader-writer locks or monitors? What about them makes them more powerful than using spinlocks, or mutexes and condition variables?
16. Can you work through the evolution of the spinlock implementations described in the Anderson paper, from basic test-and-set to the queuing lock? Do you understand what issue with an earlier implementation is addressed with a subsequent spinlock implementation?

### P3L5
17. What are the steps in sending a command to a device (say packet, or file block)? What are the steps in receiving something from a device? What are the basic differences in using programmed I/O vs. DMA support?
18. For block storage devices, do you understand the basic virtual file system stack, the purpose of the different entities? Do you understand the relationship between the various data structures (block sizes, addressing scheme, etc.) and the total size of the files or the file system that can be supported on a system?
19. For the virtual file system stack, we mention several optimizations that can reduce the overheads associated with accessing the physical device. Do you understand how each of these optimizations changes how or how much we need to access the device?

### P3L6
20. What is virtualization? What’s the history behind it? What’s hosted vs. bare-metal virtualization? What’s paravirtualization, why is it useful?
21. What were the problems with virtualizing x86? How does protection of x86 used to work and how does it work now? How were/are the virtualization problems on x86 fixed?
22. How does device virtualization work? What a passthrough vs. a split-device model?

### P4L1
23. What’s the motivation for RPC? What are the various design points that have to be sorted out in implementing an RPC runtime (e.g., binding process, failure semantics, interface specification… )? What are some of the options and associated tradeoffs?
24. What’s specifically done in Sun RPC for these design points – you should easily understand this from your project?
25. What’s marshalling/unmarschaling? How does an RPC runtime serialize and deserialize complex variable size data structures? What’s specifically done in Sun RPC/XDR?

### P4L2
26. What are some of the design options in implementing a distributed service? What are the tradeoffs associated with a stateless vs. stateful design? What are the tradeoffs (benefits and costs) associated with using techniques such as caching, replication, partitioning, in the implementation of a distributed service (think distributed file service).
27. The Sprite caching paper motivates its design based on empirical data about how users access and share files. Do you understand how the empirical data translated in specific design decisions? Do you understand what type of data structures were needed at the servers’ and at the clients’ side to support the operation of the Sprite system (i.e., what kind of information did they need to keep track of, what kids of fields did they need to include for their per-file/per-client/per-server data structures).

### P4L3
28. When sharing state, what are the tradeoffs associated with the sharing granularity?
29. For distributed state management systems (think distributed shared memory) what are the basic mechanisms needed to maintain consistence – e.g., do you why is it useful to use ‘home nodes’, why do we differentiate between a global index structure to find the home nodes and local index structures used by the home nodes to track information about the portion of the state they are responsible for.
30. Do you have some ideas how would you go about implementing a distributed shared memory system?
31. What’s a consistency model? What are the different guarantees that change in the different models we mentioned – strict, sequential, causal, weak… Can you work through a hypothetical execution example and determine whether the behavior is consistent with respect to a particular consistency model?

### P4L4
32. When managing large-scale distributed systems and services, what are the pros and cons with adopting a homogeneous vs. a heterogeneous design?
33. Do you understand the history and motivation behind cloud computing, and basic models of cloud offerings? Do you understand some of the enabling technologies that make cloud offerings broadly useful?
34. Do you understand what about the cloud scales make it practical? Do you understand what about the cloud scales make failures unavoidable?
