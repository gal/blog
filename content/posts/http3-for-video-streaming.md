---
title: "Evaluating Performance of HTTP/3 for Video Streaming: A Comparative Study with Previous Versions of HTTP"
date: 2023-04-03T19:56:30+01:00
draft: false
---

## Introduction

This project's aims were to evaluate the performance differences between HTTP/3 and other iterations of the HTTP protocol with regard to video streaming.

From results collected, it can be concluded that HTTP/3 is a viable alternative to HTTP/2 and HTTP/1.1 for video streaming. HTTP/3's performance was similar to that of the other protocols under identical network conditions. Results also dictate that in most scenarios, HTTP/3 provides equal or better video streaming performance than other protocols, except where latency significantly impacts the network conditions of the client-server communication.

Video streams over HTTP/3 tend to have lower initial load times than HTTP/2 and HTTP/1.1. According to Akamai, this is an important metric to assess QoE, especially concerning video abandonment rates [3]. This indicates that HTTP/3 is an adequate protocol for video streaming compared to the previous HTTP versions.
<!-- 
The experimental results found that HTTP/3 significantly improves latency to initiate a connection, and handles packet loss much more efficiently than previous versions of the HTTP spec. However, HTTP/3's bandwidth proves lower than previous versions.

With specific focus on video streaming, it was found that while streamed video segments took slightly longer to arrive to the client due to optimization issues with throughput, network scenarios where packet loss caused issues with download speed had significantly less effect on downloads over HTTP/3 vs the other two protocols.

The project also investigated the maturity of existing HTTP/3 implementations, both client and server-side, and found that while there are existing applications, there is still a lot of work to be done to make existing implementations more robust. As such, it is likely that HTTP/3's performance will continue to improve in the future, and will likely become an even more viable option for video streaming in the future. -->

## What is HTTP/3?

Its the newest iteration of the HTTP application layer protocol, used to transfer data over the internet. It uses a new transport layer protocol, QUIC, which is a UDP-based protocol that aims to improve the speed of the web. Alternatively previous versions of HTTP use TCP as their transport protocol. As such, it can be very useful for websites on low-memory servers or with high traffic.

## Findings

<!-- ## Initial Load Time

Initial load time is an important metric to base quality of experience when viewing streamed video over the internet. A lower initial load time provides better experience for viewers, wheras a long initial load time may cause users to abandon video playback.


![](/img/http3-video-streaming/.jpg) -->

### Packet Loss

Packet loss is a common issue on the internet, especially on mobile networks (see [^1]). The QUIC transport protocol handles packet loss much more efficiently than TCP, which previous versions of the HTTP spec sit atop. This can be explained by QUIC's loss-detection mechanisms, mainly to do with packet numbers for retransmitted packets not being ambiguous, which allows for more efficient recovery of lost packets compared to TCP. This is not possible with TCP, which is a connection-oriented protocol, and as such, the loss of a single packet can cause the entire connection to be dropped.

![](/img/http3-video-streaming/loss_mean_bitrate.jpg)
*Relationship between mean video bitrate and packet loss rate*

---

![](/img/http3-video-streaming/loss_median_buffer_length.jpg)
*Relationship between median video buffer length and packet loss rate*

---

![](/img/http3-video-streaming/loss_lq_buffer_length.jpg)
*Relationship between the lower quartile of video buffer length
and packet loss rate*

---

![](/img/http3-video-streaming/loss_initial_load.jpg)
*Relationship between the rounded-up initial load time of video
and packet loss rate*

<!-- 
![Effect of Packet loss on HTTP/3](/img/quic-vs-tcp/loss/loss_http3.svg)
![Effect of Packet loss on HTTP/2](/img/quic-vs-tcp/loss/loss_http2.svg)
![Effect of Packet loss on HTTP/1](/img/quic-vs-tcp/loss/loss_http1.svg) -->

<!-- <div style="width: 55vw; transform: translateX(calc((75vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http3.svg" alt="Effect of packet loss on HTTP/3">
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http2.svg" alt="Effect of packet loss on HTTP/2" >
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http1.svg" alt="Effect of packet loss on HTTP/1.1" >
</div> -->

### Latency

<!-- ![Effect of Latency on HTTP/3](/img/quic-vs-tcp/latency/latency_http3.svg)
![Effect of Latency on HTTP/2](/img/quic-vs-tcp/latency/latency_http2.svg)
![Effect of Latency on HTTP/1](/img/quic-vs-tcp/latency/latency_http1.svg) -->

Latency refers to the time it takes for data to be sent from one endpoint to another. This is a very important metric for video streaming, as it affects the time it takes for a video segment to be downloaded and played. HTTP/3 has a significantly higher download time when latency is increased, when compared to HTTP/1.1 and HTTP/2.

This can likely be explained by the congestion-control algorithm being used by NGINX at the time of testing, based off TCP's NewReno mechanism.

![](/img/http3-video-streaming/latency_mean_bitrate.jpg)
*Relationship between mean video bitrate and round-trip latency*

---

![](/img/http3-video-streaming/latency_median_buffer_length.jpg)
*Relationship between median video buffer length and round-trip latency*

---

![](/img/http3-video-streaming/latency_lq_buffer_length.jpg)
*Relationship between the lower quartile of video buffer length
and round-trip latency*

---

![](/img/http3-video-streaming/latency_initial_load.jpg)
*Relationship between the rounded-up initial load time of video
and round-trip latency*

<!-- <div style="width: 55vw; transform: translateX(calc((75vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http3.svg" alt="Effect of latency on HTTP/3">
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http2.svg" alt="Effect of latency on HTTP/2" >
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http1.svg" alt="Effect of latency on HTTP/1.1" >
</div> -->

## Bandwidth

<!-- ![Effect of Bandwidth on HTTP/3](/img/quic-vs-tcp/bandwidth/bandwidth_http3.svg)
![Effect of Bandwidth on HTTP/2](/img/quic-vs-tcp/bandwidth/bandwidth_http2.svg)
![Effect of Bandwidth on HTTP/1](/img/quic-vs-tcp/bandwidth/bandwidth_http1.svg) -->



HTTP/3's overall bandwidth is lower than HTTP/1.1 and HTTP/2. This is likely due to the fact that the majority of QUIC's code is implemented in userspace, and as such, will be somewhat slower than TCP or UDP, when implemented at the hardware level. This difference is mostly negligible at the rates tested, but under constrained conditions, this issue becomes less relevant.

![](/img/http3-video-streaming/bandwidth_mean_bitrate.jpg)
*Relationship between mean video bitrate and bandwidth*

---

![](/img/http3-video-streaming/bandwidth_median_buffer_length.jpg)
*Relationship between median video buffer length and bandwidth*

---

![](/img/http3-video-streaming/bandwidth_lq_buffer_length.jpg)
*Relationship between the lower quartile of video buffer length
and bandwidth*

---

![](/img/http3-video-streaming/bandwidth_initial_load.jpg)
*Relationship between the rounded-up initial load time of video
and bandwidth*


<!-- <div style="width: 65vw; transform: translateX(calc((65vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http3.svg" alt="Effect of packet loss on HTTP/3">
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http2.svg" alt="Effect of packet loss on HTTP/2" >
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http1.svg" alt="Effect of packet loss on HTTP/1.1" >
</div> -->

## Conclusion

HTTP/3 is a very promising iteration of HTTP, and will likely increase in popularity as server-side implementations mature and become more accessible. As far as I can tell, it is unlikely to *completely* replace HTTP/1.1 or HTTP/2 however, as it is not backwards compatible with these versions. Instead, I believe a legacy HTTP/1.1 or HTTP/2 server act as a *sidecar* to the HTTP/3 server, supporting older clients, acting as a fallback, and informing clients of the availability of the HTTP/3 server.

To learn more about my project, see the [report](/doc/fyp/fyp.pdf).

[^1]: https://www.researchgate.net/publication/3436819_An_Empirical_Study_on_the_Capacity_and_Performance_of_3G_Networks
[^2]: https://tools.ietf.org/html/draft-ietf-quic-recovery-32#section-6.1
