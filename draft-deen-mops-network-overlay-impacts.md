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

The past decade of Internet evolution (2014-2024) has include two significant trends, firstly the global growth of video streaming and secondly, strong work within the IETF, Operating System platforms and others to develop new privacy enhancing technologies to protect the privacy of Internet users.

The work of these two groups has largely been done independently of one another, though there are a few individuals and companies that are involved with both efforts.     However, the arrival of the new privacy enhancements in consumer products and their subsequent use by streaming video viewers has created a number of friction points which are having significant impacts to the streaming video delivery ecosystem, creating frustration for users and support issue for platforms.

This document does not propose rolling back privacy enhancement for users and the authors readily ackowledge the many challenges and difficulties in improving Internet privacy into something as complex as the Internet while maintianing compatabiltiy with wildly varied applications and uses the Internet\'s users rely upon daily in their lives.

The hope in developing this document is to provide meaningful and helpful feedback from the streaming application and streaming platform operational perspective to help the enhanced privacy architecture work being done at the IETF.

## Streaming Video Architects and Privacy Enhancement Designers Working Together

This document is not intenteded to challenge the need for privacy enchancements for the Internet, instead the hope is to illustrate impacts of such changes to the billions of users of Streaming Video delivered over the Internet so that the designers of privacy enhancements and the designers of services built on them can better understand the impacts of different design choices and perhaps find ways to mitigate such impacts through altered design choices.

Given the importantance of streaming video to the Internet\'s users, network operators, streaming platform services, and many others it only makes sense that as the IETF and it\'s participants work on improving privacy for those same users that the two efforts work together for everyone\'s benefit.

## Possible Outcomes

One possible outcome might be a best design and implementation guide developed together and published by the IETF Media Operations Group.

Another possible outcome might be an IAB led study on the operational impacts of Network Overlays to help the IETF and Internet communities better understand the operational impacts of various architectural decisions along with mitigate approaches.

# Internet Privacy Enhancements

Enhancing Internet Privacy is a challenging task to do for something as complex as the running Internet and it is easy for great proposals that fix one issue to cause new issues to arise in other places.  That\'s not a reason to stop trying, but it is important to understand the consequences of changes and to find ways to manage or mitigate such impacts, ideally without weaking or rolling back the enhancements.

A popular design choice in privacy enhancements at the IETF has been the enapsulation of data inside encrypted connections.  {!RFC9000} is an excellent example of this design and introduces a protocol that is always encrypted.

## Network Overlays

Along with the use of encrypted connections another popular approach is to additionally create alterative routings and tunnels for connections which bypass the routing and other policy decisions of the ISP access network and of the public open Internet.   These alternative network policy choices have the effect of creating a Network Overlay that operates ontop of the Access Network and Open Internet, but follows its own independent set of policies.

<tt>
 &nbsp;
 R  = router
 &nbsp;                                    <--- non-overlay traffic path --->
  device ------ R -------- R ------------------------------  R ---------------- R ---- R ---- dest-node
  &nbsp;&nbsp;             \                                                             /
  &nbsp;&nbsp;              \                                                           /
  &nbsp;&nbsp;               \                                                         /
  &nbsp;&nbsp;                R ----- R ----- ingest-node ----- egrees-node ----- R --+
&nbsp;&nbsp;                                    <--- overlay traffic path --->
&nbsp;&nbsp;
&nbsp;Figure 1:  Network Overlay routing select traffic via an alternate path
</tt>

### Partitioning
Network Overlay policy changes will include an alternate routing policy since a fundamental aspect of this design is the tunneling of connections through alternate paths to enhance privacy. The reasons for this approach are discussed in the IAB document [Partitioning as an Architecture for Privacy](https://datatracker.ietf.org/doc/draft-iab-privacy-partitioning/).

### Policy Changes
Beyond alternate routing policies, network overlays often also make changes to the Source IP Address Assignment, the DNS Resolver Selection, can include protocol conversions/translations such as HTTP2->HTTP3 and HTTP2->HTTPS2+TLS, and can include IP layer changes such as IPv4->IPv6 or IPv6->IPv6 conversions.

### MASQUE
Protocols such as MASQUE {{!RFC9494}} and services built on it such as Apple\'s [iCloud Private Relay](https://www.apple.com/privacy/docs/iCloud_Private_Relay_Overview_Dec2021.PDF) are examples of Privacy Enhancing Network Overlays that involve making a number of network policy changes from the open Internet for the connections passed through them.

The IETF has discussed this situation in the past, more than 20 years ago in 2002 Middleboxes: Taxonomy and Issues {{!RFC3234}}
was published capturing the issues with Middleboxes in the network and the affects of hidden changes occuring on the network between the sender and receiver.

## Making It Easy (for Users) by Working Under the Covers

Privacy has historically been a complicated feature to add into products targeted to end users. There are many reasons for this such as trying to meet every possible scenario and use-case and trying to provide easy user access to advanced privacy frameworks and taxonomies.   Many attempts have been made, very very few have succeeed in finding success with end users.

Perhaps learning from the lesson of offering too many options the recent trend in privacy enhancements has steered torward either a very simple \"Privacy On or Off\" switch or in other cases automatically enabling or \"upgrading\" to enhance privacy.   Apple\'s iCloud Private Relay can be easily turned on with a single settings switch, while privacy features like Encrypted DNS over HTTP and upgrade from HTTP to HTTPS connections have had a number of deployments automatically enable them for users when possible.

Keep with the Keep It Simple approach, users are generally not provided with granular Network Overlay controls permitting the user to select what applications, or what networok connections the Network Overlay's policies apply to.

Also, keeping with the Keep It Simple approach the application itself has very little connection to privacy enhancing Network Overlays.  Applications generally do not have a means to detect when networking policy changes are active. Applications generally do not have a means to access policy change settings or to interact with changing them.

# Streaming Video

Streaming Video, while just one of the many different Internet applications does standout from other uses in a number of significant that perhaps merit some amount of special consideration in understanding and addressing the impacts caused by particular privacy enhancing design and service offering choices.

Firstly, Streaming Video operates at a hard to imagine scale - streaming video globally to be more than 2 billion user daily currently and continuing to grow in leaps and bounds.

Secondly, the content types delivered through streaming has evolved from  the pre-recorded low-resolution, low-bit rate, latency tolerant Video On Demand movies, TV shows, and user generated videos delivered by pioneer streaming platforms to now include low-latency 4K and 8K live sports events, while also evolving the pre-recorded content to become very resolution and high high-bit rate 4K and 8K cinema quality and High Dynamic Range (HDR) lighting.

Finally, the expectations of streaming video viewers has signiciantly evolved from the days of settling for being able to watch a movie in a PC browser.  Viewers expect to watch on any device type the want from low-end-streaming sticks that plug into USB ports, to 4K and HDR capable laptops, 4K and 8K HDR TV screens, gaming consoles, phones and many many more choices.  Viewers also expect to have the same great viewing experience while at home connected via high-speed wired Internet, high-speed WiFi, or mobile celluar 5G and even satelite Internet connections.

To meet the growth to billions of users, the growth in content type, quality and speed expectations, and the on-any-device anywhere that I am over any-network-connection expectations of users the Streaming Video technonology infrastructure has had to itself evolve signifcantly.  This is video streaming evolution work that done in the IETF, in the [Streaming Video Technology Alliance (SVTA)](https://www.svta.org/), and in a number of other technical and industry groups.

It\'s hard to over state just how much the growth of Streaming Video has contributed to the growth of the Internet.  Internet connections of hundred megabit and gigabit speeds are because of the needs of video streaming, the ongoing work on low-latency networking and ultra-low-latency video delivery are both driven by streaming video.

## Advances in Streaming Video Architecture

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

# Emerging Operational Issues with Network Overlay Policy Changes

Streaming video applications and the streaming platforms delivery content to them are beginning to encounter a variety operational problem related to the Network Overlays as users and customers of both bring them together. 

The various impacts are list later in this document but there are a few classes of issues that have been observed:

* (1) Routing changes which cause bypassing edge CDN caches on access networks to be bypassed
* (2) Routing changes which add network latency compared to edge CDN caches or access network peering connections
* (3) Forced encrypted of unencrypted HTTP2 connections to HTTP2+TLS connections
* (4) DNS Resolver choice changes resulting in less optimal CDN cache selection or bypassing of CDN load balancing direction
* (5) Changed Source IP Address for the application\'s connections to Streaming Platform Servers resulting in logging, geofencing, and session management problems
* (6) Performance and Problem determination tools \& protocols not able to traverse the alternative route tunnel impacting services ability to diagnose connection and performance problems

## Impact of Changing Network Routing and other Policies

The problem for streaming applications occurs when the underlying network properties and policies change from what is expected by the streaming application. In particular when such changes are not visible to the streaming application, which unfortuately has emerged as a common behavior of privacy enhancing technologies based on standards such as MASQUE {{!RFC9484}}

While the open Internet is a dynamic environment, changing of basic network behaviour and policies, from what is expected as seen from the streaming application,  deviate unexpectedly from what the streaming application expects disrupts the optimized streaming delivery architecture the devcei.  Changes to Network Policies such the routing, source IP address assigned to the streaming application traffic, DNS resolver choice etc.

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

# Approaches to Mitigate or Minimize Impacts

There are a number of ways Network Overlays can work with Streaming Applications to mitigate or at best minimize their impacts.

## Transparent Policy Settings

A common theme in many of the mitigation proposal is making the Network Overlay changes and behavior transparent to the Streaming Application.  This approach should not affect privacy enhancements of the Network Overlay as it doesn't alter the Network Overlay. Enabling the Streaming Application to better understand the network environment it is operating, allows the application to adapt the changes and work within them.

## Policy Change Notification

Related to better transparency of policy settings is enabling notification to applications of changes to network policies.  This would enable the streaming application to take into account the changes when they occur - for example a Network Overlay turning on or off.

## Exclusion from Policy Changes

The other approach is enabling applications to be excluded from network overlays.  This could be be done through a variety of approaches such as providing users an option on their device to exclude either by specific applications, by specific end point destinations identified by DNS name or IP address, or by some other characteristic. 

## Support Tools

One of the issues streaming platforms have run into, especially when working on connection and performance issues is that Network Overlays that only affect very specific application protocols, for example HTTP2/tcp and HTTP3/QUIC+UDP connections, is that other Internet protocols like ICMP which are fundamental in the toolkits of support desks do not traverse the Network Overlay tunnel.  Since neither HTTP2 or HTTP3 provide protocol native ways of finding \"the bad hop\" or measuring bandwidth or latency along their path, helpdesks and engineers often find they lack the necessary support tools to help customers in determining streaming problems.



## Appendix A: Detailed Impacts of Network Policy Changes

TDB - This is connected to on going active development work at the SVTA cataloging technical impacts in detail of Network Overlays.

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


# Appendix B: Network Overlays are different than VPNs

While conceptually similar in many ways to VPN (Virtual Private Network) technology, the various network overlay
technologies currently being deployed as well as new ones currently being designed by the IETF differ quite
siginificanlty from the older VPN approach they are replacing in a number of ways.

It is also work noting that one reason why the issues discussed in this document have been histrical operating issues with
regard to VPNs is that largely VPNs have not been a pervasive way to stream video.   First, many VPNs have not had very good or consistent throughput compared to the direct open Internet.  Second, many video plaforms block or deny service to VPN connections due to the very common use of VPNs to bypass geofiltering restrictions.   

Whatever the reason, it's work looking at how VPNs differ from the Network Overylays being discussed herein.

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


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge to the contributions from the Streaming Video Technology Alliance (SVTA) based on their work studing the impacts of network overlays on the streaming platforms participating in the SVTS.  In particular the contributions of Brian Paxton have been very helpful.

