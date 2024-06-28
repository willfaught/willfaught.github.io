+++
date = 2015-02-09T23:15:13-08:00
link = "https://www.infoq.com/news/2014/08/vm-containers-performance"
tags = ["computing"]
title = "Comparing Virtual Machines And Linux Containers Performance"
+++

Containers unsurprisingly perform better than virtual machines:

>The results show that Docker equals or exceeds KVM performance in every case tested. For CPU and memory performance KVM and Docker introduce a measurable but negligible overhead, although for I/O intensive applications both require tuning.

Containerization can be IaaS as well as PaaS:

>Conventional wisdom (to the extent such a thing exists in the young cloud ecosystem) says that IaaS is implemented using VMs and PaaS is implemented using containers. We see no technical reason why this must be the case, especially in cases where container based IaaS can offer better performance or easier deployment. Containers can also eliminate the distinction between IaaS and "bare metal" non-virtualized servers since they offer the control and isolation of VMs with the performance of bare metal.
