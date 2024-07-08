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
 -
    fullname: "Sanjay Mishra"
    organization: Verizon
    email: sanjay.mishra@verizon.com

normative:

informative:


--- abstract

This document examines the operational impacts to streaming video applications
caused by changes to network policies by network overlays.  The network policy
changes include IP address assignment, transport
protocols, routing, DNS resolver which in turn affect a variety of important
content delivery aspects such as latency, CDN cache selection, delivery path
choices, traffic classification and content access controls.

--- middle

# Introduction

The past decade of Internet evolution (2014-2024) has include two significant trends, the global expansion of video streaming and a variety of privacy enhancing technologies that alter how the Internet behaves when carrying application traffic.

Streaming video applications and the streaming platforms they get their content from run into problems when these privacy enhancing technologies change the behaviors to network connections and to the underlying Internet for the streaming video connections from devices to content sources.  Particular problems arise when such changes are done in a way that hides the changes, or even that they are going to occur from the streaming application.

This document is not intenteded to challenge the need for privacy enchancements for the Internet, instead the hope is to illustrate impacts of such changes to the billions of users of Streaming Video delivered over the Internet so that the designers of privacy enhancements and the designers of services built on them can better understand the impacts of different design choices and perhaps find ways to mitigate such impacts through altered design choices.

Protocols such as MASQUE {{!RFC9494}} and services built on it such as Apple\'s iCloud Private Relay offer enhanced privacy but do so by changing the routing and other aspects of the network connection between an application and the end points it connects to.

Streaming applications, which themselves are part of a well designed and optimized streaming delivery architectured, operating over an privacy enhanced network connection run into problems when the actual connections behaviour deviates in unexpected and often unpredictable ways from the expected connection behavior.

The IETF has discussed this situation in the past, more than 20 years ago in 2002 Middleboxes: Taxonomy and Issues {{!RFC3234}}
was published capturing the issues with Middleboxes in the network and the affects of hidden changes occuring on the network between the sender and receiver.

While more recently, the IAB has explored the design of spreading traffic across mulitple paths to enhance privacy in Partitioning as an Architecture for Privacy {{I-D.draft-iab-privacy-partitioning}} suggesting that an operating environment where an application can know and predict what the behavior of connections it makes using the underlying, partitioned network may take.


## Streaming at Scale Architecture

Internet streaming has greatly matured and diversified from its early days of viewers
watching pre-recorded 320x240, 640x480 standard definiton 480p movies to wired PCs connected
to the Internet via high-latency, low-bandwidth DSL as early DOCSIS modems.

Streaming has grown be a daily go-to video source world wide for billions of viewers
and has expanded from pre-recorded movies to encompass every type of video content
imaginable.  This growth to billions of viewers and the addition of low latency sensistive
content and new connectivity options like WiFi, Cellular and Satellite in addition to
high-speed DOCSIS and fibre is the world streaming platforms now provide service in.

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

Streaming applications are part of an end to end architecture that is optimized around achieving the best experience including low latency video delivery to viewing devices.  Obviously the open Internet can be unpredictable with momentary issues such as packet loss, congestion and other conditions but streaming architecture is desiged to do its best when such momentary problems arise.

## Impact of Changing Network Routing and other Policies

The problem for streaming applications occurs when the underlying network properties and policies change from what is expected by the streaming application. In particular when such changes are not visible to the streaming application, which unfortuately has emerged as a common behavior of privacy enhancing technologies based on standards such as MASQUE {{!RFC9484}}

While the open Internet is a dynamic environment, changing of basic network behaviour and policies, from what is expectedseen from the streaming application,  deviate unexpectedly from what the streaming application expects disrupts the optimized streaming delivery architecture the devcei.  Changes to Network Policies such the routing, source IP address assigned to the streaming application traffic, DNS resolver choice etc. all 

these , such as routing
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

## Network Overlays and Streaming

The Network Overlays being discussed in this document is a generic term to characterize any technology that changes the network
and associated services that a streaming application uses to connect to streaming service infrastructure such as CDN caches, authentication, content protection, telemetry and UI services.

The Network Overlays impacting stream have a common characteristic is the creation of a new network tunnel carrying traffic from the user device through an alternative routing to the native underlying network, which itself can be characterized as a change in the network policy for the affected traffic.

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

## Network Overlays are a form of Middle Box

In many ways, the Network Overlays being discussed are a form of Middle Box which the IETF has previously discussed the general impacts of {{RFC3234}} describing the various variations of middle boxes and capturing the analysis 

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

# Approaches to Mitigate or Minimize Impacts 

## Ideas to Mitigate Impacts

### Application notification of policy change

### Interfaces to access policy settings

### Enable Application Level Opt IN or Opt OUT


## Appendix A: Detailed Impacts

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
