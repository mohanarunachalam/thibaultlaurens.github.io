---
layout: post
title: "Calculating service availability"
description: "with MTBF, MTTD, MTTR and MTTF"
category: system
tags: 
- blog-post
---
{% include JB/setup %}

**MTBF**, **MTTD**, **MTTR** and **MTTF** are the four parameters required to calculate the availability of a service or an individual component in a specific architecture.

* MTBF is the **Mean Time Between Faults**
* MTTD is the **Mean Time To Detection**
* MTTR is the **Mean Time To Repair**
* MTTF is the **Mean Time To Failure**

Well, once we got this, there are just a few little formula to know :

>**MTBF = MTTD + MTTR + MTTF**  
>**Availabiliy = MTBF / (MTBF + MTTR)**  

For example, a component with a MTBF of 8 days and a MTTR of 3 hours will have:  
**Availability** = (8\*24) / (8\*24 + 3) = **0.985**

* * *

A good way to understand availability is to look at the downtime per year they represent:

* 90% availability = 36 days and 12 hours downtime/year
* 99% availability = 87 hours, 36 minutes downtime/year
* 99\.9% availability = 8 hours,45 minutes, 36 seconds downtime/year
* 99\.99% availability = 52 minutes,33 seconds downtime/year
* 99\.999% availability = 5 minutes,15 seconds downtime / year
* 99\.9999% availability = 31 seconds downtime/year
 