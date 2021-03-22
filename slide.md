---
title: "Arm ETM Project Progress Report"
author: ""
institute: ""
urlcolor: blue
colortheme: "beaver"
date: "March, 2021"
theme: "Heverlee"
aspectratio: 43 
lang: en-US
marp: true
---



## Last week 

- Evaluation


## Progess

- Install Pocs

- There are some challenges with the lack of installation dependencies which slow down evaluation progess

---

## Progess

- 4/11

|Concurrent Programs-BugID|concurrent bug type|Symptom|
|-|-|-|
|pbzip2-N/A|OV|segmentation fault|
|Transmission-N/A|OV|assertion failure|
|memcached-127|SAV|inconsistent results|
|cherokee-N/A|SAV|confusion in log file|
|apache-25520|SAV|inconsistent in log file|
|Python-31530|MAV|segmentation fault|
|apache-45605|MAV|assertion failure|
|aget#1-N/A|MAV|inconsistent results|
|mysql-2011 |MAV|program behaviour error|
|SQLite-1672 |DL|deadlock|
|aget#2-N/A|DL|deadlock|

---



## Plan for next week

- Make the offline analysis process more high level for later automation

---




