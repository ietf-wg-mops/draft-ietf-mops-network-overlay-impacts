---
title: "Network Overlay Impacts to Streaming Video"
abbrev: "NOISV"
category: info

ipr: trust200902
area: Internet
workgroup: ADD

stand_alone: yes
pi: [toc, sortrefs, symrefs]


docname: draft-deen-mops-network-overlay-impacts-latest
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
  github: "gitnnelg/NetworkOverlays"
  latest: "https://gitnnelg.github.io/NetworkOverlays/draft-deen-mops-network-overlay-impacts.html"

author:
 -
    fullname: "Glenn Deen"
    organization: Comcast-NBCUniversal
    email: "glenn_deen@comcast.com"

normative:

informative:


--- abstract

This document examines the operational impacts to streaming video applications
caused by changes to network policies by network overlays.  The network policy
changes incluide a wide range including changes to address assignment, transport
protocols, routing, DNS resolver which in turn affect a variety of important
content delivery aspects such as latency, CDN cache selection, delivery path
choices, traffic classification and content access controls.

--- middle

# Introduction

Internet streaming has greatly matured and diversified from its early days of viewers
watching pre-recorded 320x240, 640x480 standard definiton 480p movies to wired PCs connected
to the Internet via high-latency, low-bandwidth DSL as early DOCSIS modems.

Streaming has grown be a daily go-to video source world wide for billions of viewers
and has expanded from pre-recorded movies to encompass every type of video content
imaginable.  This growth to billions of viewers and the addition of low latency sensistive
content and new connectivity options like WiFi, Cellular and Satellite in addition to
high-speed DOCSIS and fibre is the world streaming platforms now operate in.

Streaming platform have now have significant technical challenges to meet viewer expectations:

* (1) Delivery scales that commonly range from hundreds of thousand to millions of viewers simultaneously, with billions of views world wide daily;
* (2) Low latency demands from live sports, live events and live streamed content;
* (3) content resolutions and corresponding formats which have jumped from the days of SD-480p to 4K (3840x2160) and 8K (7680x4320) along with bit rates which can had data needs of 10-24+ Mbps for 4K with 8K demanding 40 Mbps under extreme compression and 150-300 Mbps for high quality such as cinema;
* (4) devices with very divese capabilities low-cost streaming sticks, to Smart TVs, tablets, phones, and game consoles
* (5) broad range of connectivity choices including WiFi, gig speed-low latency DOCSIS, satellite, and 5G cellular networks;
* (6) application transport protocols including DASH, HLS, http2/tcp, http3/QUIC, WebRTC, Media over QUIC (MoQ) and speciality application transports such as SRT, HESP etc.

To meet these techincal challenges streaming platforms have significantly invested in developing delivery architectures that
are built with detailed understandings of each element in the content delivery pathway from the content capture all the way
through to the screen of the viewer.

Having an understanding and a predicatablity of the delivery path is essential for streaming operators and the introduction
of network overlays based on technologies such as MASQUE especially when they are designed to not be easily detected, even
by applications using them has created a new set of technical problems for streaming operators and network operators and
for the viewers that subscribe to them.

The core problem occurs when changes to network policies are made, often without notification or visibiltiy to applications and
without clear methods of probing to determine and test changed behaviors that affect the streaming application\'s content
delivery path resulting in increased latency, changes of IP address for the application as seen by either the application
or the streaming service connection, changes to DNS resolvers being queried and the results returned by DNS, and changes to
application transports such as adding or removing outter layer encryption are all problems that have been observed in
production streaming platforms.

This document dicusses the various operational impacts and attempts to provide a few preliminary approachs that could
help mitigate the impacts.

# Network Overlay Functionality

The Network Overlays being discussed in this document is a generic term to describe technology that change an
applications network behaviors such as the routing path taken by application traffic, which network services
such as DNS resolvers are used by the application and can include changes to IP address for the device or the
IP address of the application appears from the perspective of connection end-points.

The overall effect is to create a tunnel, that often applies to only particular protocol transports such as HTTP
but not varients such as HTTPS or HTTP and HTTPS but not other network protocol traffic from the device.
The traffic affected by these overlays typically takes an alternate routing from the device to an ingest point,
followed by one or more hops ac


<tt>Example 1.  Network Overlay routing select traffic via an alternate path 
 &nbsp;
 R  = router
 &nbsp;                                    <--- non-overlay traffic path --->
  device ------ R -------- R ------------------------------  R ---------------- R ---- R ---- dest-node
  &nbsp;&nbsp;             \                                                             /
  &nbsp;&nbsp;              \                                                           /
  &nbsp;&nbsp;               \                                                         /
  &nbsp;&nbsp;                R ----- R ----- ingest-node ----- egrees-node ----- R --+
&nbsp;&nbsp;                                    <--- overlay traffic path --->
</tt>


## Network Overlays are different than VPNs

While conceptually similar in many ways to VPN (Virtual Private Network) technology, the various network overlay
technologies currently being deployed as well as new ones currently being designed by the IETF differ quite
siginificanlty from the older VPN approach they are replacing in a number of ways.

### VPNs typically:

* (1) VPNs typically are detectable by both the video application and often by the streaming platform.
* (2) VPNs typically work at the network layer of a device, resulting in a wide-range (if not all) transports
*  and protocols from the devive flowing through the VPN
* (3) VPNs typically provide exception options allowing for exclusion from traversing via the VPN based on
* various criteria such as application, destination IP address, application protocol etc.

### Network Overlays typically:

* (1) Network Overlays are often undetectable by video applications or by the streaming plaform, when in use
* (2) Network Overlays often only apply to specific application transports such as HTTP2/TCP or HTTP3/QUIC while not applying to HTTP2/TCP+TLS on the same device.
* (3) Network Overlays often only apply to HTTP connections and do not support ICMP, non-http versions of DNS, NTP etc, and various tools used for network measurement, problem determination, and network manangement that are not http based.
+ (4) Network Overlays do not expose to applications any means for the application to discover the policy changes the overlay will apply to the applications network connections.
+ (5) Network Overlays do not expose mechanisms or APIs for applications to interact with them such as getting or setting options.


## Network Overlay Impacts to Streaming Video

As discussed Streaming Video delivery is done at scale using highly optimized delivery paths and infrastructure choices.  The priamry impact comes from the unpredicatable changes overlays make that alter this optimized delivery path.   

### Improper Cache Selection

### Alternate Routing and Unexpected Routing

### Bypass of Native Network Policies

### Incorrect Traffic Classification

### Increased Latency

### Impacts of Altered IP Address

### Performance management

### Problem Determination

### Altered logging

### Geofilters breakage

#### unintended blocking

## Broken login/authentication

## Ideas to Mitigate Impacts

### Application notification of policy change

### Interfaces to access policy settings

### Enable Application Level Opt IN or Opt OUT


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

This work is connected to work at the Streaming Video Technology Alliance (SVTA) and is submitted to the IETF based on experiences and observations of a variety of impacts to streaming video delivery of network overlays to streaming video study done by the SVTA

TODO acknowledge.
