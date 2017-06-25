---
layout: post
title: "Calculating service availability"
description: "with MTBF, MTTD, MTTR and MTTF"
category: system
---
{% include JB/setup %}

MTBF, MTTD, MTTR and MTTF are the four parameters required to calculate the availability of a service or an individual component in a specific architecture.

- `MTBF`: Mean Time Between Faults
- `MTTD`: Mean Time To Detection
- `MTTR`: Mean Time To Repair
- `MTTF`: Mean Time To Failure

* * *

Once we got this, we can calculate service availability with the following:

```
MTBF = MTTD + MTTR + MTTF
Availabiliy = MTBF / (MTBF + MTTR)
```

Example: a component with a MTBF of 8 days and a MTTR of 3 hours will have:  
Availability = (8 \* 24) / (8 \* 24 + 3) = 0.985

* * *

Service availability is often referred in `nines`. The table below shows the downtime per year and per day allowed according to availabilty and uptime. 


| Availability | Uptime   | Downtime/Year | Downtime/Day     |
| ------------ | -------- | ------------- | ---------------- |
| 1 Nine       | 90%      | 36.5 days     | 2.4 hours        |
| 2 Nines      | 99%      | 3.65 days     | 14 minutes       |
| 3 Nines      | 99.9%    | 8.76 hours    | 86 seconds       |
| 4 Nines      | 99.99%   | 52.6 minutes  | 8.6 seconds      |
| 5 Nines      | 99.999%  | 5.25 minutes  | 0.86 seconds     |
| 6 Nines      | 99.9999% | 31.5 seconds  | 8.6 milliseconds |
{: .table .table-bordered .table-hover .table-condensed}