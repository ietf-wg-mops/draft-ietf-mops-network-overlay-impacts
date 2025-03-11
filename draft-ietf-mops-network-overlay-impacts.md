---
title: "Network Overlay Impacts to Streaming Video"
abbrev: "NOISV"
category: info

ipr: trust200902
area: Internet
workgroup: MOPS

stand_alone: yes
pi: [toc, sortrefs, symrefs]


docname: draft-ietf-mops-network-overlay-impacts-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Media OPerationS"
keyword:
 - network policy
 - video streaming
 - streaming
venue:
  group: "Media OPerationS"
  type: "Working Group"
  mail: "mops@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/mops/"
  github: "ietf-wg-mops/draft-ietf-mops-network-overlay-impacts"
  latest: "https://ietf-wg-mops.github.io/draft-ietf-mops-network-overlay-impacts/draft-ieft--mops-network-overlay-impacts.html"

author:
 -
    fullname: "Glenn Deen"
    organization: Comcast-NBCUniversal
    email: "glenn_deen@comcast.com"
 -
    fullname: "Sanjay Mishra"
    organization: Verizon
    email: sanjay.mishra@verizon.com

normative:

informative:

--- abstract

This document examines the operational impacts to streaming video applications
caused by changes to network policies by network overlays.  The network policy
changes include IP address assignment, transport protocols, routing, DNS resolver
which in turn affect a variety of important content delivery aspects such as latency,
CDN cache selection, delivery path choices, traffic classification and content access controls.

--- middle

# Introduction

Enhancing the privacy of Internet users has a been significant focus of the IETF since the Snowden revelations and the publication of {{!RFC7258}}.  {{!RFC7264}} explored in greater detail the technical threats identified in RFC7258 along with high level descriptions of mitigations.
Since then the various working groups at the IETF have endeavored to address the specific threats to their respective area and have produced a long list of new RFCs with privacy enhancements deliberately and consciously included.
Protocols like QUIC {{!RFC9000}} are examples of the new generation of IETF protocols with privacy enhancements such as always enabled encryption built directly into their design.

At the same time that the IETF has been diligently enhancing Internet privacy, Internet video streaming has become a part of daily life for billions of viewers with streaming being for many their primary way of watching sports, entertainment, user generated content (UGC) and news.
This has grown to become the primary data by volume traversing the Internet with an hour of HD video consisting of roughly 1.5-2.5GB of data and streaming is estimated to account for 80-85% of current global Internet traffic.

The Operational Considerations for Streaming Media {{!RFC9317}} provides a good introduction to the various engineering aspects encountered by streaming platforms in engineering and operating the infrastructure used to meet the global growth in video streaming.

While early streaming efforts were satisfied with being able to stream a video to a device successfully without consideration for efficiency or scale, the rapid growth in viewership has pushed platforms to develop sophisticated architectures and designs.

For video that is prerecorded, such as Video On Demand (VOD) TV and Movie content and User Generated Content (UGC), distributing the recorded and encoded content using Content Delivery Network (CDN) caches is a common technique to meet demand at scale.
The IETF CDNi working group has done significant work on this and SVTA OpenCaching (svta.org) extends upon CDNi to create a robust implementation of CDN architecture for VOD scaling.
VOD and UCG streaming typically place a focus on selecting the best CDN cache with the best responsiveness, the shortest network path and fewest of hops to avoid congestion and bandwidth limitations that might limit the quality of the video being delivered.

The newest frontier in streaming is live streaming, primarily around sports events.  Live streaming, like VOD, has significant capacity demands with events that can have viewership levels ranging from tens of thousands to 10-50 million live viewers.
The day of hundreds of millions of live viewers for a single event such as a major global sporting event is on the near horizon, with no limit in sight on how far growth can go.
Perhaps one day a significant portion of the global population will view live events over the Internet.

Even today, the current viewer levels for large events are pushing the boundaries of video streaming techniques.
Live also comes with new challenges; CDN caching is still used to meet scaling challenges for live events, but unlike VOD, it can't be prerecorded and prepositioned on CDN caches ahead of the event.
Live sports streaming also has important low latency requirements - viewers don\'t want a big goal spoiled by alerts on their phones, or cheering in the street before they see it on their own screen.
The video pipeline used to deliver live streamed events with low latency and at high quality is very optimized and as a result is very sensitive to interference from unexpected network behaviors and conditions.

The delivery pipelines of VOD, UGC and Live have each been engineered and optimized to deliver the highest quality, in the most efficient manner over the Internet from platform to viewers.
However, as vendors have increasingly responded to [RFC7258] and [RFC7264] in consumer devices and services, they have occasionally introduced unexpected and sometimes subtle changes to the network behavior and the video pipeline in ways that can interfere with and undermine the efficiencies, scaling and low latency engineering that video platforms have spent considerable time, money and talent developing and deploying to meet user video experience expectations.

The authors readily acknowledge the many challenges and difficulties in improving Internet privacy in an area as complex as the Internet while also maintaining compatibility with the wildly varied applications and uses of the Internet on which users rely upon daily in their lives. This is hard stuff and it\'s very natural for there to be operational considerations that must be understood and folded back into architectural designs and consumer products.

This document is intended to document the various impacts that have been observed by streaming applications from a streaming platform operational perspective on certain enhanced privacy architecture approaches that have been pursued at the IETF.


# Internet Privacy Enhancements

The IETF\'s work to enhance the privacy of Internet and defend against pervasive monitoring as described in {{!RFC7258}} has employed as series of techniques
starting with encrypting network data flows, typically using TLS.  Other approaches involve changing things at a policy level, such as routing or DNS resolver choices, so as to further obfuscate and isolate the network and data flows from the underlying base network as means of interfering with pervasive
monitoring.

{{!RFC7258}} from the IAB examines various pervasive montoring approaches while {{!RFC7624}} discusses responses that enhance privacy.  {{!RFC9000}} itself is an excellent example of the applied design approaches and introduces the QUIC transport protocol that is always encrypted.

## Network Overlays

The IETF\'s privacy enhancement work to address {{!RFC7258}} covers a lot of design choices and policies such as the approach of always-on encryption as shown in the design of QUIC {{!RFC9000}}.   Many of these do not affect video streaming, however those that do impact streaming fall into a class of design choices that can be described as creating a new network overlay, operating as an overlay on top of the underlying native network, but  following one or more different policies than the underlying network or the streaming application would follow on their own.

The Network Overlays that have been found by video platform operators to impact streaming operations are those that make policy changes in ways that are not directly visible, selectable, or detectable by the video streaming application or streaming platform.  These changes, when made under the covers and out of visibiltiy to the streaming application, often can make unexpected changes to the streaming pipeline in ways that undermine the architecture choices of the streaming application and platform engineers.

Network Overlays that cause network connection behavior and properties to differ from what the application expects can lead to situations where the application user and operator assume one set of behaviors of the network data flow to be true, while in reality one or more different behaviors may occur. This in turn can lead to unanticipated outcomes that can have operational impacts.

Protocols such as MASQUE {{!RFC9484}} and services built on it such as Apple\'s [iCloud Private Relay](https://www.apple.com/privacy/docs/iCloud_Private_Relay_Overview_Dec2021.PDF) are examples of Privacy Enhancing Network Overlays that involve making a number of network policy changes from the open Internet for the connections passed through them.

### Emerging Operational Issues with Network Overlay Policy Changes

Streaming video applications and the streaming platforms delivering content are starting to encounter various operational challenges related to Network Overlays.
Typically the primary problems are encountered when the network overlay has made policy changes that are either unexpected, are difficult or impossible for the streaming platform to detect, or the changes are inconsistently applied.

There are a variety of impacts but a few common classes of issues have been observed:

## Policy Changes

Changing the encryption policy from that expected by the streaming application, for example changing HTTP urls in manifests into HTTPS, can disrupt architectures which involve the network being able to detect video flows.

Changing routing policy from what is expected by the streaming application can break CDN cache selection logic, resulting in a farther away cache deliverying lower quality video at higher latency than the closer cache that would be selected by the CDN cache selection logic.

A routing policy change example is illustated in figure 1 with different policies for a network overlay vs the underlying base network, which the changes the traffic path from the Network Overlay having a different routing policy from that of the underlying native base network.

~~~
 R  = router
                  <--- non-overlay traffic path --->
 device -- R ---- R ------------- R ------------- R ---- R -- dest-node
            \                                           /
             \                                         /
              \                                       /
               R -- R -- ingest -- egress -- R ------+
                     <--- overlay traffic path --->

 Figure 1:  Network Overlay routing select traffic via an alternate path
~~~

### Partitioning

Network Overlay policy changes include an alternate routing policy since a fundamental aspect of this design is the tunneling of connections through alternate paths to enhance privacy. The reasons for this approach are discussed in the IAB document [Partitioning as an Architecture for Privacy](https://datatracker.ietf.org/doc/draft-iab-privacy-partitioning/).

### Procotol Policy Changes

Network overlays have been observed to make application and transport protocol changes from what is expected. Changes such as HTTP2/tcp into HTTP3/QUIC and HTTP2 into HTTPS2+TLS are performed by some privacy enhancing approaches, converting what is considered an undersirable protocol choice into what is considered a better alternative, hidden under the covers from the application.

One impact occurs when the protocol change alters the network as seen by the video application.  For instance, a video application may make a test fetch of video inorder to test measure network conditions which will be use to make streaming decisions for the actual content being accessed. If the application test probe uses HTTP2/tcp to test, but the actual content access request over HTTP2/tcp is converted to HTTP3/QUIC then the video platform does not have accurate test probe results which can directly lead to erroneous, non-optimal choices by the video player algorithm.

### Encryption Policy

Changing the encryption policy applied to video streams, either adding where it wasn't originally used or removing if it was originally specified, can cause a wide range of operational problems.

#### Forced Encryption Upgrade

Changing unencrytped HTTP2 to encrypted HTTP2+TLS connections will prohibit streaming workflows that involve content detection as part of the network delivery.  This
can result in video traffic not being correctly identified and the incorrect network policies being applied to it.  This is particularly problematic in mobile and multicast environments.

#### Forced Encryption Downgrade

Equally so, removing of encryption applied to the transport stream by a streaming platform would be significantly problematic as such encryption may be part of a content protection and content integrity protections architecture.

### Address Policy Changes

IP address changes, such as converting from IPv4 to IPv6 or IPv6 to IPv4, done unexpectantly or done invisibly to the application, can cause both routing and cache selection issues, as well as complicating debugging efforts, leading engineers to use incorrect addresses when analyzing logs, conducting test probes, etc.

Source IP Address assignment changes, again when done invisibly to the application, can cause significant disruption.  Platform authentication gateways that associate session authorizations with the session's device IP address can result in service access denial when associated addresses change unexpectedly.  For example, when the device address as seen by the video application is different from the device address seen by the associated streaming platform, this can result in the platform rejecting logins, content access and other service functions from the device.

### DNS Policy Changes

Network overlays that change DNS settings have long been an issue for CDN architectures that use DNS as part of their load balancing architecture.

#### EDNS0

EDNS0 extension information was specifically designed to help CDN cache selection logic by providing more information to the decision making algorithms, so a policy change that changes the DNS resolver for an appliction to a different resolver that does not support EDNS0 can have quite a significant impact to a video application.

### Log Data Changes

Logging is often the first tool used to find and diagnose problems.   Network overlays which change policies that result in unexpected and non-understandable log entries on either the user device or the video platform can greatly undermine the use of logs in problem determination and resolution.

### Geo Location & Identification

Network Overlays that change the apparent location of devices can result in platforms from not being able to properly identify the geospatial location of the user.
It is very common for CDN caches to apply IP address level geolocation to determine in broad terms, such as identifying the country the user is in.
If an overlay changes the apparent origin addresses of video device to one outside the of the address blocks mapped by location providers, then geolocation can fail and users can be denied access to content they otherwise are able to access.

### CDN interconnection troubleshooting

In a CDN interconnection, when 2 CDN domains have to localize a point of failure, they first determine the delivery path and a point of observation where to do measurement. They then use a dichotomous approach to identify the domain where the failure point lies. The issue with overlay networking is the following : CDNs use
their request routing information to determine a point of observation on the delivery path where the measurement should be conducted. The flow cannot be observed at the observation point because the delivery path is overwritten by the re-routing of the overlay network.

### Routing Changes

Routing changes, which can cause connection issues between video applications and the infrastructure services they use, can create a large number of problems.

#### End to End Problem Discovery

A common issue in video delivery is locating where along the delivery path the video transport is encountering problems.  Often, such problems are more complex than
just a connection not working. Instead, they involve identifying bottlenecks, lost packets, and congestion issues.  When the routing changes from what is expected or
visible to support tools, it becomes an operational trouble spot for users and platform support to locate and determine the source of the problems.

#### CDN Edge Cache Selection due to Routing

A significant and often overlooked problem is the addition of network latency when compared to connecting with edge CDN caches or access network peering connections.  Routing changes can bypass edge CDN caches and instead choose less optimal caches.

~~~
 R  = router
           <--- non-overlay traffic path --->
 device -- R ---- R ---- Edge CDN Cache
            \
             \
              \
               R --- R -- ingest -- R --- R -- egress -- R ------R ---- Less Optimal CDN Cache
                     <--- overlay traffic path --->

 Figure:  Routing Changes altering CDN Cache selection
~~~

#### Performance and Problem determination

Network overlays often interfere with performance and problem determination tools. This interference occurs because the tools and protocols may not be able to traverse the alternative route tunnel, impacting the service's ability to diagnose connection and performance issues. Additionally, the network overlay itself may not support the tools or their functions.

#### Impact of Changing Network Routing and other Policies

The problem for streaming applications arises when the underlying network properties and policies deviate from what the streaming application expects, especially when these changes are hidden or not visible to the application.

While the open Internet is inherently dynamic, unexpected changes to fundamental network behaviors and policies, as perceived by the streaming application, can disrupt the optimized delivery architecture designed for end-user devices. Alterations in network policies, such as routing, source IP address assignment, and DNS resolver selection, significantly influence this behavior.

Understanding the delivery path is crucial for streaming operators. The introduction of network overlays, like those using technologies such as MASQUE, particularly when designed to be undetectable by the applications, has created new technical challenges for streaming operators, network operators, and viewers.

The core problem occurs when changes to network policies are made, often without notification or visibility to applications. These changes lack clear methods of probing to determine and test the altered behaviors that affect the streaming application's content delivery path. This results in increased latency, changes in the IP address for the application as seen by either the application or the streaming service connection, changes to DNS resolvers being queried, and the results returned by DNS. Additionally, changes to application transports, such as adding or removing outer layer encryption, have all been observed in production streaming platforms.

# Policy Changes Hidden from Applications

One of the central recurring challenges with streaming applications operating on devices or networks affected by network overlays is that these policy changes are frequently concealed from the applications.

Applications often find it difficult or even impossible to detect when network policy changes will be active and what they are changing.  For example, a device may have a designated default DNS resolver, but may have a different resolver selected depending on how the streaming application queries DNS.

Likewise, a streaming application might find that queries to one application transport protocol, such as HTTP, will have one set of routing policies applied to it, but a different application transport like HTTPS may have a different set of routing policies applied.

Streaming applications that cannot determine the exact expected behavior can prevent the streaming application from making good content source decisions, and can prevent appllications from being able to provide reliable feedback and logs when problems are encountered.

# Making It Easy (for Users) by Working Under the Covers

Historically, incorporating privacy features into consumer-facing products has been complex. This challenge arises from the need to address a wide range of use cases while also offering users easy access to advanced privacy frameworks and taxonomies. Many attempts have been made and very few have achieved success with end users.

Perhaps learning from the lessons of offering too many options, the recent trend in privacy enhancements has steered toward either a very simple \"Privacy On or Off\" switch, or in other cases, automatically enabling or \"upgrading\" to enhance privacy.   Apple\'s iCloud Private Relay can be easily turned on with a single
settings switch, while privacy features such as \"Encrypted DNS over HTTP\" and \"upgrade from HTTP to HTTPS connections\" are often automatically enabled for users when possible.

Keeping with the motto of "Keep It Simple", users are generally not provided with granular Network Overlay controls permitting the user to select what applications or network connections the Network Overlay's policies can apply to.

Adhering to the "Keep It Simple" approach, the application itself has very little connection to privacy enhancing Network Overlays.  Applications generally do not have a means to detect when networking policy changes are active. Applications generally lack the ability to access or modify policy settings.

# Streaming Video

Streaming Video, although one of many Internet applications, is unique in several significant ways. These distinctions may warrant special consideration when evaluating and addressing the impacts of specific privacy-enhancing design and service choices.

Firstly, streaming video operates on an immense scale, serving over 2 billion users globally each day, with numbers continuing to grow rapidly.

Secondly, streaming content has advanced from early low-resolution, low-bit rate, latency-tolerant video-on-demand movies, live or pre-recorded TV shows, and user-generated videos to now include low-latency 4K and 8K live sports events. Additionally, pre-recorded content has evolved to feature high-bit rate formats such as cinema quality 4K and 8K with High Dynamic Range (HDR) lighting.

Finally, the expectations of streaming video viewers have significantly evolved from the days of settling for being able to watch a movie in a PC browser.  Viewers expect to watch on any device type they want ranging from low-end-streaming sticks that plug into an HDMI port, to 4K and HDR capable laptops, 4K and 8K HDR TV
screens, gaming consoles, smart phones and many more choices.  Viewers also expect a seamless viewing experience, whether they are at home using high-speed wired Internet, high-speed WiFi, mobile 5G, or even satellite Internet connections.

To accommodate the exponential growth to billions of users, the increasing variety of content types, higher quality and speed expectations, and the demand for seamless access on any device over any network connection, the Streaming Video technology infrastructure has had to evolve significantly.  This video streaming evolution work is being done in the IETF, in the [Streaming Video Technology Alliance (SVTA)](https://www.svta.org/), and in a number of other technical and industry groups.

It\'s hard to overstate just how much the growth of streaming video has contributed to the growth of the Internet.  Today's internet connections, ranging from hundreds of megabits to gigabit speeds, are driven by the demands of video streaming. Additionally, ongoing advancements in low-latency networking and ultra-low-latency video delivery are propelled by the requirements of streaming video.

## Advances in Streaming Video Architecture

Internet streaming has greatly matured and diversified from its early days of viewers
watching pre-recorded 320x240, 640x480 standard definition 480p movies to wired PCs connected
to the Internet via high-latency, low-bandwidth DSL and early DOCSIS modems.

Streaming has grown to the extent that it has become a daily go-to video source worldwide for billions of viewers,
and has expanded from pre-recorded movies to encompass every type of video content
imaginable.  The expansion to billions of viewers, coupled with the inclusion of low-latency sensitive content and diverse connectivity
options such as WiFi, Cellular, Satellite, high-speed DOCSIS, and fiber, defines the current landscape in which streaming platforms operate.

With the large user base and its usage, Streaming platforms also have significant technical challenges to meet viewer expectations:

1. Delivery scales that commonly range from hundreds of thousands to many millions of viewers simultaneously, with billions of global views each day;
2. Low-latency demands from live sports, live events and live streamed content;
3. Content resolutions and formats which have evolved significantly from the days of SD-480p to 4K (3840x2160) and 8K (7680x4320), along with increased bit rates, with 4K requiring 10-24+ Mbps and 8K demanding 40 Mbps under extreme compression, and up to 150-300 Mbps for high-quality cinema-grade content;
4. Devices with very diverse capabilities from low-cost streaming sticks, to Smart TVs, tablets, phones, and game consoles;
5. Broad range of connectivity choices including WiFi, Gig speed low-latency DOCSIS, Fiber, satellite, and 5G cellular networks;
6. Application transport protocols including MPEG DASH, HLS, HTTP2/TCP, HTTP3/QUIC, WebRTC, Media over QUIC (MoQ) and specialty application transports such as SRT, HESP, etc.

To meet these challenges, streaming platforms have significantly invested in developing delivery architectures that
are built with detailed understandings of each element in the content delivery pathway, starting from the content capture all the way through to the screen of the viewer.

Streaming applications are part of an end-to-end architecture that is optimized around achieving the best experience, including low-latency video delivery to viewing devices.  The open Internet can be unpredictable with temporary issues like packet loss, congestion and other conditions. However, streaming architecture is designed to handle these momentary problems as effectively as possible, often through use of dynamic adaptive approaches designed into streaming protocols and platform components.

# Middleboxes and learning from the past

The IETF addressed similar concerns over two decades ago in the 2002 publication, "Middleboxes: Taxonomy and Issues" {{!RFC3234}}. This document highlighted the challenges posed by middleboxes in the network and the impact of hidden changes occurring between the sender and receiver.

# Appendix A: Network Overlays are different than VPNs

While conceptually similar in many ways to VPN (Virtual Private Network) technology, the various network overlay
technologies currently being deployed as well as new ones currently being designed by the IETF differ quite
significantly in a number of ways from the older VPN approach they are replacing.

It is also worth noting that one reason why the issues discussed in this document have not been concerned about
VPNs is that VPNs have largely not been a pervasive way to stream video.   First, many VPNs have not had very good or consistent throughput compared to the direct open Internet and so provide a poor viewing experience.  Second, many video platforms block or deny service to VPN connections due to the very common use of VPNs to bypass geofiltering restrictions.

Whatever the reason, it's worth looking at how VPNs differ from the Network Overlays being discussed herein.

## VPNs typically:

1. VPNs are typically detectable by the video application, and often by the streaming platform.
2. VPNs typically operate at the network layer of a device, resulting in a broad range of (if not all) transports
and protocols from the device flowing through the VPN.
3. VPNs typically offer exception options, enabling exclusion from VPN traversal based on
criteria such as application, destination IP address, or application protocol.

## Network Overlays typically:

1. Network Overlays are often undetectable by video applications or the streaming platform when in use.
2. Network Overlays often only apply to specific application transports, such as HTTP2/TCP or HTTP3/QUIC, while not applying to HTTP2/TCP+TLS on the same device.
3. Network Overlays typically apply only to HTTP connections and do not support protocols such as ICMP, non-HTTP DNS, NTP, and various non-HTTP-based tools used for network measurement, troubleshooting, and management.
4. Network Overlays do not expose any means for the application to discover policy changes that the overlay will apply to the application's network connections.
5. Network Overlays do not expose mechanisms or APIs for applications to interact with them, such as getting or setting options.

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge to the contributions from the Streaming Video Technology Alliance (SVTA) based on their work studying the impacts of
network overlays on the streaming platforms. In particular, contributions from Brian Paxton have been very helpful.

