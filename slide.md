---
title: "Arm ETM and PMU Project Group Progress Report"
author: "Yiming Zhang"
institute: "COMPASS"
urlcolor: blue
colortheme: "beaver"
date: "March 16, 2021"
theme: "Heverlee"
aspectratio: 43 
lang: en-US
marp: true
---

## Plan of Last Week

- According to Haonan's document to hook some libc to get more record to help data flow construction

- Find Root cause of concurrency bug from prototype

- Overhead evaluation

---


## Library Fuction hook

- Using ***LD_PRELOAD*** modify the linkage of shared libraries and the resolution of symbols at runtime.

- Hook *New/Delete* to get the heap address which is not recorded by sysdig.

```Bash
thread_id_1417 :call lib function new and the value: 0x55555916d0 with size=48

thread_id_1417 :call lib function new and the value: 0x5555591710 with size=48

thread_id_1417 :call lib function new and the value: 0x5555591750 with size=48

thread_id_1417 :call lib function new and the value: 0x5555591790 with size=72
...
```

---

## Prototype of concurrency bug from pbzip2

- Crash position

```Bash
Timestamp - 357993179327945
<_Z8consumerPv>
3628:  f9402fe0   ldr  x0, [sp,#88]             x0=0, sp,#88=0x5555591790, [sp,#88]=0
362c:  f9401800   ldr  x0, [x0,#48]             x0,#48=48
3630:  97fff904   bl  1a40 <pthread_mutex_unlock@plt>
Timestamp - 357994169257407
```

we can determine the key failure object position according to the signal SIGSEGV, Segmentation fault from coredump 

```Bash
3628:  f9402fe0   ldr  x0, [sp,#88]  -> <_Z8consumerPv>_sp,#88
```
---

## Find the corresponding instructions with order from flow analysis

![Flow graph from Inclusion-based Points-to Analysis](flow_graph.png)

---

## Find Root cause

Find Root cause  according to corresponding control flow and data flow with order between main and consumer thread.

```Bash
Context - Context ID = 0x589
<_Z11queueDeleteP5queue>
3c00:  f9400fe0   ldr  x0, [sp, #24]         x0=0x5555591790, sp,#24=0x7FFFFFF6B8, [sp,#24]=0x5555591790
3c10:  97fff7cc   bl  1b40 <_ZdlPvm@plt>     [0x5555591790]=0
3c14:  f9000fff   str  xzr, [sp, #24]        [sp, #24]=0

Context - Context ID = 0x597
<_Z8consumerPv>:
3628:  f9402fe0   ldr  x0, [sp, #88]    x0=0, sp,#88=0x5555591790, [sp,#88]=0
362c:  f9401800   ldr  x0, [x0, #48]    x0,#48=48
3630:  97fff904   bl  1a40 <pthread_mutex_unlock@plt>
```

---

## Overhead evaluation

- Using UNIX Benchmarks

||||||
|-|-|-|-|-|
||None|ETM|Sysdig|ETM+sysdig|
|System Call Score|420179.2 lps|420794.4 lps||420361.7 lps|
|System Benchmarks Index Score|489.5|489.0||487.2|
|System Call Overhead|||||
|System Benchmarks Overhead|||||

---

## Plan

- POCs collection and Evaluation
- Switch to SSD and test the overhead
