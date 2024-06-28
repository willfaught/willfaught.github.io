+++
categories = ["technology"]
date = 2023-07-29T14:18:05-07:00
description = "Metrics for understanding request-driven program performance."
link = "https://www.infoworld.com/article/3638693/the-red-method-a-new-strategy-for-monitoring-microservices.html"
tags = ["metrics", "microservices", "red"]
title = "RED Metrics"
+++

Tim Yocum, writing for InfoWorld:

>The RED method is a monitoring methodology coined by Tom Wilkie based on what he learned while working at Google. RED is derived from some best practices established at Google known as the “Four Golden Signals,” developed by Google's SRE team.
>
>[...]
>
>RED stands for rate, errors, and duration. These represent the three key metrics you want to monitor for each service in your architecture:
>
>- Rate - The number of requests the service is handling per second.
>- Error - The number of failed requests per second.
>- Duration - The amount of time each request takes.
>
>Using these three metrics, you can get a solid understanding of how your services are performing. The number of requests gives you a baseline for how much traffic is going to your service. The portion of those requests that are errors lets you know if a service is functioning within your SLO. Finally, the duration of time it takes for each request to be handled by your service gives you insight into the overall user experience of your application.

Rob Ewaschuk, writing for Google, on those [four golden signals](https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals):

>Latency
>
>The time it takes to service a request. It's important to distinguish between the latency of successful requests and the latency of failed requests. For example, an HTTP 500 error triggered due to loss of connection to a database or other critical backend might be served very quickly; however, as an HTTP 500 error indicates a failed request, factoring 500s into your overall latency might result in misleading calculations. On the other hand, a slow error is even worse than a fast error! Therefore, it's important to track error latency, as opposed to just filtering out errors.
>
>Traffic
>
>A measure of how much demand is being placed on your system, measured in a high-level system-specific metric. For a web service, this measurement is usually HTTP requests per second, perhaps broken out by the nature of the requests (e.g., static versus dynamic content). For an audio streaming system, this measurement might focus on network I/O rate or concurrent sessions. For a key-value storage system, this measurement might be transactions and retrievals per second.
>
>Errors
>
>The rate of requests that fail, either explicitly (e.g., HTTP 500s), implicitly (for example, an HTTP 200 success response, but coupled with the wrong content), or by policy (for example, "If you committed to one-second response times, any request over one second is an error"). Where protocol response codes are insufficient to express all failure conditions, secondary (internal) protocols may be necessary to track partial failure modes. Monitoring these cases can be drastically different: catching HTTP 500s at your load balancer can do a decent job of catching all completely failed requests, while only end-to-end system tests can detect that you're serving the wrong content.
>
>Saturation
>
>How "full" your service is. A measure of your system fraction, emphasizing the resources that are most constrained (e.g., in a memory-constrained system, show memory; in an I/O-constrained system, show I/O). Note that many systems degrade in performance before they achieve 100% utilization, so having a utilization target is essential.
>
>In complex systems, saturation can be supplemented with higher-level load measurement: can your service properly handle double the traffic, handle only 10% more traffic, or handle even less traffic than it currently receives? For very simple services that have no parameters that alter the complexity of the request (e.g., "Give me a nonce" or "I need a globally unique monotonic integer") that rarely change configuration, a static value from a load test might be adequate. As discussed in the previous paragraph, however, most services need to use indirect signals like CPU utilization or network bandwidth that have a known upper bound. Latency increases are often a leading indicator of saturation. Measuring your 99th percentile response time over some small window (e.g., one minute) can give a very early signal of saturation.
>
>Finally, saturation is also concerned with predictions of impending saturation, such as "It looks like your database will fill its hard drive in 4 hours."
