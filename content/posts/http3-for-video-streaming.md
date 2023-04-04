---
title: "Evaluating Performance of HTTP/3 for Video Streaming: A Comparative Study with Previous Versions of HTTP"
date: 2023-04-03T19:56:30+01:00
draft: false
---

## Introduction

This project investigates the performance and possible advantages of HTTP/3 over previous iterations of HTTP, specifically with regard to video streaming. During research carried out, performance of HTTP/3 was compared with the performance of earlier versions of HTTP, focusing primarily on the variable network parameters of bandwidth, latency and packet loss.

The experimental results found that HTTP/3 significantly improves latency to initiate a connection, and handles packet loss much more efficiently than previous versions of the HTTP spec. However, HTTP/3's bandwidth proves lower than previous versions.

With specific focus on video streaming, it was found that while streamed video segments took slightly longer to arrive to the client due to optimization issues with throughput, network scenarios where packet loss caused issues with download speed had significantly less effect on downloads over HTTP/3 vs the other two protocols.

The project also investigated the maturity of existing HTTP/3 implementations, both client and server-side, and found that while there are existing applications, there is still a lot of work to be done to make existing implementations more robust. As such, it is likely that HTTP/3's performance will continue to improve in the future, and will likely become an even more viable option for video streaming in the future.

## What is HTTP/3?

Its the newest iteration of the HTTP application layer protocol, used to transfer data over the internet. It uses a new transport layer protocol, QUIC, which is a UDP-based protocol that aims to improve the speed of the web. Alternatively previous versions of HTTP use TCP as their transport protocol. As such, it can be very useful for websites on low-memory servers or with high traffic.

## Findings

### Packet Loss

![Effect of Packet loss on HTTP/3](/img/quic-vs-tcp/loss/loss_http3.svg)
![Effect of Packet loss on HTTP/2](/img/quic-vs-tcp/loss/loss_http2.svg)
![Effect of Packet loss on HTTP/1](/img/quic-vs-tcp/loss/loss_http1.svg)

<!-- {{< galleryrow dir="/img/quic-vs-tcp/loss" >}} -->
<!-- <div style="width: 55vw; transform: translateX(calc((75vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http3.svg" alt="Effect of packet loss on HTTP/3">
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http2.svg" alt="Effect of packet loss on HTTP/2" >
    <img style="display: inline-block; max-width:350px; height: 300px" src="/img/quic-vs-tcp/loss/loss_http1.svg" alt="Effect of packet loss on HTTP/1.1" >
</div> -->
<small>Y-axis is time in seconds</small>

---

Packet loss is a common issue on the internet, especially on mobile networks (see [^1]). The QUIC transport protocol handles packet loss much more efficiently than TCP, which previous versions of the HTTP spec sit atop. This can be explained by QUIC's FEC (Forward Error Correction) mechanism, which allows for the recovery of lost packets. This is not possible with TCP, which is a connection-oriented protocol, and as such, the loss of a single packet can cause the entire connection to be dropped.

### Latency

![Effect of Latency on HTTP/3](/img/quic-vs-tcp/latency/latency_http3.svg)
![Effect of Latency on HTTP/2](/img/quic-vs-tcp/latency/latency_http2.svg)
![Effect of Latency on HTTP/1](/img/quic-vs-tcp/latency/latency_http1.svg)

<!-- <div style="width: 55vw; transform: translateX(calc((75vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http3.svg" alt="Effect of latency on HTTP/3">
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http2.svg" alt="Effect of latency on HTTP/2" >
    <img style="display: inline-block; width:400px; height: 350px" src="/img/quic-vs-tcp/latency/latency_http1.svg" alt="Effect of latency on HTTP/1.1" >
</div> -->

Latency refers to the time it takes for data to be sent from one endpoint to another. This is a very important metric for video streaming, as it affects the time it takes for a video segment to be downloaded and played. HTTP/3 has a significantly higher download time when latency is increased, when compared to HTTP/1.1 and HTTP/2. This can likely be explained by the loss-detection mechanism defined in section 6.1 of the QUIC recovery spec [^2].

> A packet is declared lost if it meets all the following conditions:
>
> - The packet is unacknowledged, in-flight, and was sent prior to an acknowledged packet.
> - The packet was sent kPacketThreshold packets before an acknowledged packet (Section 6.1.1), or it was sent long enough in the past (Section 6.1.2).

This loss-detection mechanism is based on the number of packets sent before the packet in question, and the time since the packet was sent. As such, when latency is increased, the time since the packet was sent increases, and the packet may be declared lost. This packets to be renegotiated, and the download time increases. This is not the case with HTTP/1.1 and HTTP/2, which do not have a loss detection mechanism, and as such, the connection is not reset.

## Bandwidth

![Effect of Bandwidth on HTTP/3](/img/quic-vs-tcp/bandwidth/bandwidth_http3.svg)
![Effect of Bandwidth on HTTP/2](/img/quic-vs-tcp/bandwidth/bandwidth_http2.svg)
![Effect of Bandwidth on HTTP/1](/img/quic-vs-tcp/bandwidth/bandwidth_http1.svg)

<!-- <div style="width: 65vw; transform: translateX(calc((65vw - 100vw)/2)); display: flex; align-items: center; justify-content: space-around"> 
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http3.svg" alt="Effect of packet loss on HTTP/3">
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http2.svg" alt="Effect of packet loss on HTTP/2" >
    <img style="display: inline-block; max-width:400px; height: 350px" src="/img/quic-vs-tcp/bandwidth/bandwidth_http1.svg" alt="Effect of packet loss on HTTP/1.1" >
</div> -->

HTTP/3's overall bandwidth is lower than HTTP/1.1 and HTTP/2. This is likely due to the fact that the majority of QUIC's code is implemented in userspace, and as such, will be somewhat slower than TCP or UDP.

## Conclusion

HTTP/3 is a very promising iteration of HTTP, and will likely increase in popularity as server-side implementations mature and become more accessible. As far as I can tell, it is unlikely to *completely* replace HTTP/1.1 or HTTP/2 however, as it is not backwards compatible with these versions. Instead, I believe a legacy HTTP/1.1 or HTTP/2 server will be a sidecar to the HTTP/3 server, supporting older clients, and informing clients of the availability of the HTTP/3 server.

To learn more about my project, see my [extended abstract](/doc/fyp/extended_abstract.pdf).

[^1]: https://www.researchgate.net/publication/3436819_An_Empirical_Study_on_the_Capacity_and_Performance_of_3G_Networks
[^2]: https://tools.ietf.org/html/draft-ietf-quic-recovery-32#section-6.1
