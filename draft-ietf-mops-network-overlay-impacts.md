---
title: "Network Overlay Impacts to Streaming Video"
abbrev: "NOISV"
category: info

ipr: trust200902

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
  latest: "https://ietf-wg-mops.github.io/draft-ietf-mops-network-overlay-impacts/draft-ietf-mops-network-overlay-impacts.html"

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
  PRIVATE-RELAY:
    target: https://www.apple.com/privacy/docs/iCloud_Private_Relay_Overview_Dec2021.PDF
    title: iCloud Private Relay Overview
    author:
      org: Apple Inc.
    date: 2021-12

--- abstract
This document examines the operational impacts on streaming video applications resulting from network policy changes introduced by network overlays. Such overlays may alter IP address assignment, transport protocols, routing behavior, or DNS resolution. These changes can, in turn, affect critical aspects of content delivery, including latency, CDN cache selection, delivery path optimization, traffic classification, and content access controls.

--- middle

# Introduction

This document explores the unintended operational impacts of network
overlays, such as VPNs and MASQUE-based tunnels, on highly scalable
Internet streaming applications. Because these streaming architectures
are optimized for specific network environments, overlay-enforced policy
changes can degrade performance.

The authors acknowledge the inherent friction between maximizing
Internet transport privacy and maintaining the operational efficiency of
data-intensive applications. Integrating these competing operational
requirements into architectural designs is a complex task.

The purpose of this document is to establish a clear problem statement
regarding these operational impacts. It documents the negative
externalities observed by streaming platforms when privacy-enhancing
overlays or unexpected network policy changes disrupt the delivery path.
This analysis aims to provide application developers, platform
architects, network operators, and protocol designers with a shared
framework for understanding how overlay mechanisms impact predictable
video delivery. Defining specific mitigations is out of scope for this
document.

# Streaming Applications: Optimized for Scaling and Latency

Internet video streaming has become a global utility, evolving by
necessity into a highly optimized operational ecosystem. This ecosystem
delivers live sports, entertainment, linear television, user-generated
content (UGC), and breaking news to any Internet-connected device across
heterogeneous networks, including high-bandwidth fixed lines, mobile,
and Wi-Fi. {{?RFC9317}} reports that video made up about 75% of traffic
to end users in 2019.

These sessions require sustained, data-intensive throughput,
particularly at high-definition (HD) and 4K resolutions.

To support this volume, the video streaming ecosystem relies on
sophisticated network and data management methodologies. This includes
the IETF's Content Delivery Network Interconnection (CDNI) frameworks
{{?RFC6707}} {{?RFC7336}} for tiered Content Delivery Network (CDN)
topologies and downstream CDN selection/orchestration. Each streaming
platform carefully architects its delivery pipeline around predictable
network behavior and routing. This optimization is critical to meet
scaling demands regarding simultaneous viewer concurrency, aggregate
data volume, and the ultra-low latency thresholds required for
interactive and live sports viewing. The foundations of this application
ecosystem are discussed in {{?RFC9317}}, though the technology has
continued to evolve significantly since its publication.

Regardless of content type, the successful delivery of data at this
scale requires a well-understood, end-to-end application and networking
architecture. The workflow behaves predictably and consistently
according to the design assumptions of the video streaming architects.

# Network Overlays: Privacy Enhancements and Operational Features

Enhancing Internet user privacy has been a core focus of the IETF
following the publication of {{?RFC7258}}, which established pervasive
monitoring as a technical attack. {{?RFC7624}} further detailed these
technical threats and outlined high-level mitigation approaches. Since
then, IETF working groups have systematically addressed these vectors,
producing new standards with native privacy protections. Protocols like
QUIC {{?RFC9000}} exemplify this shift, embedding always-on transport
encryption and wire-image obfuscation directly into the protocol design.

Concurrently, network overlays, such as Virtual Private Networks (VPNs)
and MASQUE-based tunnels, have emerged as easily deployable mechanisms
to shield user traffic from network intermediaries. However, as consumer
operating systems, browsers, and applications increasingly integrate
these privacy-enhancing overlays, they introduce architectural changes
to the underlying network behavior.

Privacy is not the only motivation for deploying network overlays.
Enterprises commonly require VPNs or Secure Web Gateways (SWGs) on
employee devices, including when employees work from home, and these
deployments may operate in either split-tunnel or full-tunnel mode
(see {{split-tunnel}}). Overlays are also used to obtain IPv6
connectivity from access networks that offer only IPv4, for example
using tunnel brokers {{?RFC3053}} or Teredo {{?RFC4380}}. Many of the
operational impacts described in this document can also arise in
these deployments. Some of these motivations, such as enterprise
policy, are outside the influence of the network operator.

These overlays frequently obfuscate client IP geolocation, alter path
MTU characteristics, disrupt standard anycast routing, add latency and
jitter, or mask flow identifiers. Such changes directly interfere with
or undermine the traffic engineering, localized edge-caching, load
balancing, and low-latency optimizations that streaming platforms rely
upon.

# Internet Privacy Enhancements

The IETF's efforts to strengthen Internet privacy and mitigate pervasive
monitoring have driven a series of architectural and protocol-level
developments. The initial focus was on encrypting network data flows,
most commonly through the wider adoption of Transport Layer Security
(TLS). Over time, these efforts have expanded to include changes at the
policy and design level, such as modifying routing paths, selecting
privacy-preserving DNS resolvers, and introducing encrypted transport
protocols, to better obscure and isolate user traffic from observation
within the underlying network infrastructure.

Collectively, these privacy-enhancing measures have reshaped how
networks and applications interact. However, they also introduce new
considerations for operational visibility, traffic management, and
performance optimization, which are particularly relevant to streaming
video applications.

## Network Overlays

While many of these privacy enhancements have minimal
impact on video streaming, some introduce new behaviors that can
be described as creating network overlays, which are logical
networks that operate on top of the underlying native network but
apply different routing, transport, or policy decisions than
either the native network or the streaming application would
independently choose.

Network overlays that alter policies or paths in ways not directly
visible, selectable, or detectable by the streaming application or
platform can have significant operational effects. These overlays
may silently modify network properties, such as source IP
addresses, DNS resolver choices, or routing behavior, without the
knowledge of the streaming service or end user. Such hidden policy
changes can inadvertently disrupt the assumptions underlying
adaptive streaming architectures, content delivery path
optimization, or CDN selection mechanisms.

When a network overlay modifies connection properties in ways that
differ from application expectations, the result can be mismatched
assumptions between the application and the actual transport
environment. This disconnect may cause degraded performance,
misclassification of network paths, or unexpected latency and throughput
characteristics, all of which affect streaming quality and operational
predictability.

Protocols such as MASQUE {{?RFC9298}} {{?RFC9484}} and services built on
it such as Apple's iCloud Private Relay {{PRIVATE-RELAY}} illustrate
privacy-enhancing network overlays that deliberately alter connection
policies relative to the open Internet. While beneficial for user
privacy, such mechanisms can also obscure the visibility and control
that streaming services rely on for consistent content delivery and
Quality of Experience (QoE) management.

## Transparency and Connection Policy

What matters when considering network overlay impact on streaming
is not the technology or protocol used, but whether the
alternative network connection policies applied are transparent
or hidden from the connection endpoints.

Traditionally, connection policy changes tended to be
transparent to the application-server connection. Changes made to
the connection were visible to one or both sides, enabling the
connection endpoints to have awareness of the policies applied.

The issue this document focuses on is where alternative network
connection policies are non-transparent to the connection
endpoints, particularly the application. The application is the
party architecturally designated to make decisions about network
connection properties and policies, and non-transparent overlays
remove that ability without any indication to the application that
this has occurred.

This distinction means that even classic connection policy
approaches such as traditional VPNs fall within this document's
problem statement if they operate non-transparently to the
connection endpoints, and particularly to the application.

Network overlays also affect end-to-end connection autonomy. When an
application opens a connection, it does so based on server-published
endpoint information, obtained via DNS or direct IP addressing.
Non-transparent overlays silently intercept that connection, rerouting
or readdressing it and making new path choices without the application
being involved. Neither the application nor the server has signaled
consent to this interception, and neither endpoint receives any
indication that the path or addressing has changed. This silent
substitution of routing decisions is distinct from a transparent VPN,
where the application is aware that its traffic is traversing an overlay
and the network operator has explicitly configured that behavior.

Historically, the issues discussed in this document have not been a
major concern for typical VPN deployments, largely because VPNs
have not been a pervasive way to stream video. Many VPNs have not
offered throughput or consistency comparable to a direct Internet
path, and many video platforms block or degrade service to detected
VPN connections due to their common use in bypassing geofiltering
restrictions.

Traditional and newer overlay deployments have tended to differ
along the following lines.

### Transparent Connection Policy Approaches

These approaches:

1. are generally detectable by the application and the
   network operator.
2. typically work at the network layer of a device, so a wide
   range, if not all, of the device's transports and protocols flow
   through the overlay.
3. typically provide exception options allowing traffic to be
   excluded based on criteria such as application, destination IP
   address, or application protocol.

### Non-transparent Connection Policy Approaches

These approaches:

1. are often undetectable by video applications or the
   streaming platform while in use.
2. often apply only to specific applications or traffic types, such
   as browser traffic, leaving other traffic on the same device
   unaffected.
3. often apply only to HTTP connections, without support for
   ICMP, non-HTTP DNS, NTP, or the other non-HTTP-based tools used
   for network measurement, problem determination, and network
   management.
4. do not expose to applications any means of discovering what
   policy changes the overlay applies to their network connections.
5. do not expose mechanisms or APIs for applications to interact
   with the overlay, such as getting or setting options.

These are patterns of observed deployment behavior, not categories
defined by protocol specification. What determines whether a
connection policy mechanism falls within the scope of this document
is whether it operates non-transparently to the connection
endpoints, not the protocol or technology it uses.

Even where a network overlay operates transparently to the
connection endpoints, the operational impacts described in this
document, including protocol changes, tunneling effects, and path
alterations, remain significant considerations for streaming video
deployments.

### Full-Tunnel and Split-Tunnel Configurations {#split-tunnel}

A related but separate distinction is whether an overlay carries all
of a device's traffic (full tunnel) or only a subset (split tunnel).
{{?RFC7556}} describes both configurations in terms of Provisioning
Domains (PvDs), using the terms "force tunneling" and "split
tunneling".

Some of the issues described in {{dns-policy-changes}} and
{{cdn-edge-cache-selection}} can arise specifically in split-tunnel
configurations where DNS resolution and the resulting traffic take
different paths. For example, if a DNS query is answered by the
network operator's resolver and the media traffic is then carried
over the overlay, a CDN that maps clients to caches using DNS may
direct the client to a cache inside the network operator's network.
The media traffic then leaves the overlay at its egress point and
travels back to that cache, adding latency and extra traffic between
the overlay egress point and the network operator's network. The
reverse mismatch, where DNS is resolved through the overlay and the
traffic is sent outside it, can lead the CDN to select a cache near
the overlay egress point rather than near the client.

When DNS and the associated traffic follow the same path, as in a
full-tunnel configuration or a multi-PvD implementation that uses
DNS results only within the PvD that provided them
({{Section 5.2.1 of RFC7556}}), the CDN can select a cache near the
overlay egress point. This may move traffic that could otherwise be
served from caches inside the network operator's network onto its
external peering or transit links, but the cache selection is
consistent with the path the traffic actually takes.

## Policy Changes

Streaming video applications and content delivery platforms are
increasingly encountering operational challenges associated with network
overlays. These challenges arise when overlays introduce policy changes
that are unexpected, inconsistently applied, or difficult or impossible
for the streaming platform to detect or adapt to in real time. While the
specific impacts vary depending on the overlay's design and
implementation, several common classes of operational issues have been
observed across deployments. These include mismatches in routing and
cache selection, unexpected transport-layer behavior, and
inconsistencies in latency or throughput reporting that affect QoE
monitoring and optimization.

Changes to network policies introduced by overlays can alter the
expected behavior of streaming applications in several ways.

For example, an overlay that modifies encryption policies, such as
transforming HTTP URLs in manifests into HTTPS connections, can disrupt
architectures that rely on the network's ability to identify or classify
video flows. In such cases, the visibility of traffic used for caching,
optimization, or Quality of Service (QoS) treatment may be reduced or
lost entirely.

Similarly, overlays that alter routing policies can interfere with the
CDN cache selection logic used by streaming platforms. A change in
routing path may cause the application to connect to a more distant
cache, resulting in higher latency, lower throughput, and degraded video
quality, even when a closer cache would otherwise have been selected.

An example of a routing policy change is illustrated in
{{fig-overlay-routing}}, showing how a network overlay can apply a
routing policy that diverges from that of the underlying base network,
resulting in a modified traffic path and different delivery
characteristics.

~~~
 R  = router
                  <--- non-overlay traffic path --->
 device -- R ---- R ------------- R ------------- R ---- R -- dest-node
            \                                           /
             \                                         /
              \                                       /
               R -- R -- ingest -- egress -- R ------+
                     <--- overlay traffic path --->
~~~
{: #fig-overlay-routing title="Network Overlay Routing Selects Traffic via an Alternate Path"}

### Partitioning

Network overlay policy changes often include the use of alternate
routing policies, as a core element of their design involves tunneling
connections through different network paths to enhance user privacy and
reduce tracking. This architectural concept, partitioning, is further
discussed in {{?RFC9614}}. By isolating traffic and obscuring its
correlation with the underlying native network, partitioning helps
defend against pervasive monitoring and traffic analysis.

While effective for privacy protection, these routing partitions can
also alter network visibility and path selection in ways that affect
streaming video performance, such as cache selection accuracy, latency,
and adaptive bitrate (ABR) responsiveness.

### Protocol Policy Changes

Network overlays have been observed to alter application and
transport protocols from those originally selected by the
streaming application. In some cases, privacy-enhancing or
optimization mechanisms automatically translate connections, for
example converting HTTP/2 over TCP into HTTP/3 over QUIC, or
upgrading cleartext HTTP/1.1 sessions to HTTPS. Such
conversions are typically performed to enforce stronger privacy,
security, or efficiency policies, but they may occur without
visibility or control by the streaming application.

A key operational impact arises when protocol substitution changes the
network characteristics perceived by the video application. A video
application may perform a preliminary fetch to measure network
conditions before selecting an appropriate bitrate for content delivery.
If the application's test probe uses HTTP/2 over TCP but the subsequent
content request is silently converted by the overlay to HTTP/3 over
QUIC, the measured results no longer reflect the actual transport path.
This mismatch can lead to inaccurate bandwidth estimation, causing the
ABR algorithm to select non-optimal streaming parameters and degrade
user experience.

### Encryption Policy

Changes to the encryption policy applied to video streams, whether
by adding encryption where it was not originally used or by
removing or terminating encryption where it was expected, can
introduce significant operational challenges for streaming
applications and delivery networks.

In some cases, network overlays or privacy-enhancing systems may
automatically enforce encryption, converting plaintext HTTP video
traffic into HTTPS or encapsulating transport flows within encrypted
tunnels. While this improves confidentiality, it can also obscure
traffic classification and disable optimizations that rely on visibility
into flow metadata, such as CDN cache selection, adaptive bitrate
tuning, or QoS marking.

Conversely, if encryption is removed or terminated prematurely, such as
through a proxy that decrypts and re-encrypts video traffic, it can
violate end-to-end security assumptions made by the application or CDN,
potentially exposing content or user data to unauthorized inspection.

In both cases, mismatched encryption policies between the streaming
application, CDN, and the underlying network can lead to reduced
performance, incorrect cache usage, or inconsistent delivery behavior.

#### Forced Encryption Upgrade

Enforcing encryption upgrades, for example converting cleartext HTTP/1.1
traffic into HTTPS, can disrupt streaming workflows that rely on the
network's ability to inspect or classify content as part of the delivery
process. When network visibility into streaming flows is removed,
content-aware optimizations such as CDN cache selection, multicast
distribution, or traffic prioritization may fail to function as
designed. As a result, video traffic may be misclassified as generic
encrypted data, leading to incorrect policy enforcement or suboptimal
delivery behavior.

This issue is particularly significant in mobile and multicast-based
environments, where network-assisted detection of video streams is often
required to achieve efficient bandwidth utilization and maintain quality
of experience. In such cases, forced encryption upgrades may prevent the
network from applying appropriate delivery optimizations, resulting in
degraded performance or increased operational complexity.

#### Forced Encryption Downgrade {#forced-encryption-downgrade}

Conversely, removal or termination of encryption originally
applied by a streaming platform can introduce serious operational
and security concerns. In many streaming architectures,
transport-level encryption, such as HTTPS or QUIC, is not only
used to ensure confidentiality but also forms an integral part of
the content protection and integrity assurance mechanisms.

When an intermediate network overlay or proxy terminates TLS sessions or
otherwise downgrades an encrypted connection to plaintext, it can
invalidate end-to-end trust assumptions between the client, CDN, and
content provider. Such behavior may expose sensitive metadata, enable
unauthorized content inspection or modification, and break Digital
Rights Management (DRM) protections.

In effect, a forced encryption downgrade undermines both security and
operational reliability, leading to potential playback failures, content
delivery errors, or loss of user trust.

### Address Policy Changes

Network overlays that modify IP addressing policies, such as
converting IPv4 to IPv6, IPv6 to IPv4, or reassigning source IP
addresses, can introduce a range of operational challenges for
streaming platforms, particularly when these changes occur
unexpectedly or are invisible to the application. Such address
changes can disrupt routing decisions, CDN cache selection, and
traffic localization processes that depend on stable endpoint
addresses. They also complicate diagnostic and troubleshooting
efforts, as engineers analyzing logs, performing test probes, or
correlating session data may inadvertently use incorrect or
outdated IP information.

A related issue arises when the source IP address observed by the
streaming platform differs from that seen by the client
application or device. Because many streaming architectures use
IP-based session binding, such as platform authentication gateways
that associate user or device authorization with a specific IP
address, unannounced address translation can result in service
access failures, login rejections, or denied content delivery. For
example, when an overlay reassigns or masks the client's IP
address, the streaming platform may interpret this as a new or
unauthorized connection, even though the client session remains
active. This mismatch can lead to intermittent playback
interruptions, degraded user experience, or increased operational
complexity for both service providers and network operators.

Associating authorization with a client IP address is fragile even
without overlays, because a client's source address can legitimately
vary between connections in the same session. Examples include
dual-stack clients that use both IPv4 and IPv6 {{?RFC8305}}, NAT
deployments that map a client's connections to different external
addresses {{?RFC4787}}, and IPv6 temporary addresses {{?RFC8981}}.
Whether an overlay triggers this failure can also depend on whether
it is configured as a full tunnel or a split tunnel
({{split-tunnel}}). This document describes the operational impact
of this practice and does not evaluate it.

Overlays can also interact with IPv4/IPv6 transition technologies.
If an overlay uses IPv4 as its underlay on an IPv6-only or
IPv6-mostly {{?RFC8925}} access network, the overlay traffic depends
on the network's IPv4 transition mechanism, such as NAT64
{{?RFC6146}}, MAP-T {{?RFC7599}}, or Lightweight 4over6
{{?RFC7596}}, even when the streaming service itself is reachable
over IPv6. During large streaming events, this can place additional
load on the translation or encapsulation gateways.

### DNS Policy Changes {#dns-policy-changes}

Network overlays that modify DNS resolver settings or redirect DNS
queries can have significant implications for CDNs that rely on
DNS-based load balancing for cache selection and traffic localization.

Many CDN architectures determine the best cache for a client by
observing the source IP address of the DNS resolver making the
request. When an overlay substitutes or masks the resolver, either
intentionally or as part of privacy-enhancing policies, the CDN
may incorrectly infer the client's location, resulting in
non-optimal cache selection, increased latency, or reduced video
quality.

#### EDNS Client Subnet

The EDNS Client Subnet (ECS) option {{?RFC7871}}, carried in the
Extension Mechanisms for DNS (EDNS(0)) {{?RFC6891}}, allows recursive
resolvers to include information about the client's network in DNS
queries, improving CDN cache selection accuracy. If a network overlay
redirects DNS queries to a resolver that does not support ECS or
deliberately omits this information, the CDN loses critical context for
determining the most appropriate edge cache. This can lead to the
selection of a distant or overloaded cache, negatively impacting video
startup time, buffering, and overall user experience.

### Log Data Changes

Accurate and consistent logging is essential for diagnosing
streaming performance and operational issues. Network overlays
that alter connection properties, such as DNS resolvers, IP
addresses, or transport protocols, can cause log entries to differ
between the client device and the streaming platform. When such
discrepancies occur, engineers attempting to correlate logs for
troubleshooting may misinterpret session behavior or fail to
identify the true source of a problem. Unexpected or misleading
log data therefore undermines both problem determination and
root-cause analysis, complicating operational monitoring and
incident response workflows.

### Geolocation and Identification {#geolocation-and-identification}

Network overlays that alter the apparent source location of user devices
can interfere with streaming platforms' ability to accurately determine
geospatial attributes such as country, region, or network domain.

Many CDNs and content providers rely on IP address based
geolocation to enforce regional content licensing, apply local
regulations, or select nearby caches for optimal performance.
When an overlay substitutes or masks the client's IP address,
presenting it as originating from a different region or outside of
known geolocation mappings, the platform may be unable to
correctly associate the user with their actual location.

This can result in users being denied access to region-restricted
content that they would otherwise be authorized to view, or being
directed to distant CDN caches, causing degraded video quality and
higher latency.

In addition, such location ambiguity complicates analytics, fraud
detection, and rights management processes that depend on consistent
geographic identifiers.

### CDN Interconnection Troubleshooting

In CDN interconnection scenarios, when two CDN domains collaborate
to localize a point of failure, they typically begin by
identifying the delivery path and selecting observation points
along that path to take diagnostic measurements. Through iterative
testing, they narrow down the problem domain to isolate the
failure's location.

However, when network overlays alter routing behavior, this
process becomes unreliable. CDNs depend on their request routing
information to determine where along the delivery path
measurements should be taken. The presence of an overlay that
reroutes or tunnels traffic means that the expected observation
point no longer lies on the actual traffic path. As a result, the
flow cannot be observed where the CDN expects it to be, making
fault localization and coordination between interconnecting CDNs
significantly more difficult.

### Routing Changes

Routing changes introduced by network overlays can alter the
expected path between video applications and the infrastructure
services they rely on. Such changes may cause a wide range of
operational problems, including degraded performance, inconsistent
latency, or failures in CDN cache selection and session
persistence.

When routing behavior differs from what the video platform or
application expects, content delivery optimizations such as
proximity-based cache selection, adaptive bitrate decisions, and
transport-layer congestion management can become ineffective. These
effects can be difficult to detect, as the overlay's routing policy is
often not visible to the streaming application or operators monitoring
network performance.

#### End-to-End Problem Discovery

A common issue in video delivery is locating where along the
delivery path the video transport is encountering problems. Such
problems are often more complex than a connection not working at
all and instead involve identifying bottlenecks, lost packets, and
congestion issues. When routing changes from what is expected or
visible to support tools, it becomes an operational trouble spot
for users and platform support to locate and determine the source
of the problems.

#### CDN Edge Cache Selection due to Routing {#cdn-edge-cache-selection}

A significant and often overlooked problem is the addition of
network latency compared to connecting directly to edge CDN caches
or access network peering connections. Routing changes that cause
traffic to bypass edge CDN caches and instead reach less optimal
caches are illustrated in {{fig-cdn-cache-selection}}.

~~~
 R  = router
           <--- non-overlay traffic path --->
 device -- R ---- R ---- Edge CDN Cache
            \
             \
              \
               R -- R -- ingest -- R -- R -- egress -- R -- Less Optimal
                                                            CDN Cache
                     <--- overlay traffic path --->
~~~
{: #fig-cdn-cache-selection title="Routing Changes Altering CDN Cache Selection"}

#### Performance and Problem Determination

Network overlays often interfere with the tools used in
performance and problem determination. This is due to either the
tools and protocols not being able to traverse the alternative
route tunnel, impacting a service's ability to diagnose connection
and performance problems, or the network overlay itself not
supporting or carrying the tool's functions.

#### Impact of Changing Network Routing and Other Policies

The problem for streaming applications occurs when the underlying
network properties and policies change from what the streaming
application expects, especially when such changes are hidden or
not visible to the application.

While the open Internet is a dynamic environment, changes to basic
network behavior and policies that deviate unexpectedly from what
the streaming application expects disrupt the optimized streaming
delivery architecture for the end-user device. Changes to network
policies such as routing, source IP address assignment, and DNS
resolver choice influence this behavior.

Having a reliable understanding of the delivery path is essential
for streaming operators. The introduction of network overlays,
particularly those designed to be undetectable by the applications
using them, has introduced new technical challenges for streaming
operators, network operators, and their viewers.

The core problem occurs when changes to network policies are made
without notification or visibility to applications and without
clear methods for probing or testing the changed behaviors. The
affected behaviors include increased latency, changes to the IP
address seen by either the application or the streaming service,
changes to DNS resolvers and the results they return, and changes
to application transports such as adding or removing encryption.
All of these have been observed in production streaming platforms.

### Unintended Content Blocking {#unintended-content-blocking}

A strongly undesirable consequence of network policy changes is
the blocking of content to the viewer. This may affect primary
content URLs, or possibly advertising fetched from a second URL
alongside the main video content. Such blocking can be due to
policy changes altering device IP addresses, or routing changes
that conflict with enforced traffic routing policies.

Such blocking may be connected to restrictions built upon data feeds
used for geofiltering and georestrictions, for example restrictions that
block delivery to networks identified as commercial data centers or CDN
service network addresses. In effect, the traffic conflicts with
security configurations that expect streaming viewers to be on home or
mobile networks, not in commercial data centers or CDN content networks.
This is more likely to occur in network overlays that shift egress
traffic to commercial or CDN address blocks.

This is a particularly difficult problem to diagnose as it may
appear inconsistently from one streaming session to another.
Small changes in URLs in manifests from one session to another are
especially problematic on streaming platforms that use multi-CDN
delivery, where different delivery and security protection
policies from different CDN operators may be encountered.

# Policy Changes Hidden from Applications

One of the central recurring challenges with streaming applications
operating on devices or networks affected by network overlays is
that these policy changes are frequently concealed from the
applications.

Applications often find it difficult or even impossible to detect
when network policy changes will be active and what they are
changing. For example, a device may have a designated default DNS
resolver, but a different resolver may be selected depending on how
the streaming application queries DNS.

Likewise, a streaming application might find that one application
transport protocol such as HTTP has one set of routing policies
applied to it while a different transport such as HTTPS has a
different set of routing policies applied.

Streaming applications that cannot determine the expected behavior
are prevented from making good content source decisions and from
providing reliable feedback and logs when problems are
encountered.

# Making It Easy (for Users) by Working Under the Covers

Historically, incorporating privacy features into consumer-facing
products has been complex. This challenge arises from the need to
address a wide range of use cases while also offering users easy
access to advanced privacy frameworks and taxonomies. Many
attempts have been made and very few have found success with end
users.

Perhaps learning from the lessons of offering too many options,
the recent trend in privacy enhancements has steered toward either
a very simple "Privacy On or Off" switch, or in other cases,
automatically enabling or upgrading to enhance privacy. Apple's
iCloud Private Relay can be easily turned on with a single
settings switch, while privacy features such as DNS over HTTPS
(DoH) {{?RFC8484}} and upgrades from HTTP to HTTPS connections
have seen several deployments that automatically enable them for
users when possible.

In keeping with this "Keep It Simple" approach, users are
generally not provided with granular network overlay controls
permitting them to select which applications or network
connections the overlay policies apply to. Applications likewise
have very little connection to privacy-enhancing network overlays.
They generally have no means to detect when networking policy
changes are active, and no means to access or change the policy
settings.

# Streaming Video

Streaming video, while just one of the many different Internet
applications, stands out from other uses in several significant
ways that merit consideration when understanding and addressing
the impacts caused by particular privacy-enhancing design and
service offering choices.

Streaming video operates at a very large scale that continues to
grow.

The content types delivered through streaming have evolved from
pre-recorded low-resolution, low-bitrate, latency-tolerant
video-on-demand movies, live or pre-recorded TV shows, and user
generated videos delivered by pioneering streaming platforms to
now including low-latency 4K and 8K live sports events, while
also evolving pre-recorded content to high-bitrate 4K and 8K
cinema quality and High Dynamic Range (HDR) video.

The expectations of streaming video viewers have also
significantly evolved from the days of watching a movie in a PC
browser. Viewers expect to watch on any device they want, ranging
from low-end streaming sticks that plug into an HDMI port to 4K and
HDR capable laptops, 4K and 8K HDR TV screens, gaming consoles,
and smartphones. Viewers also expect a seamless viewing
experience, whether they are at home using high-speed wired
Internet, high-speed Wi-Fi, mobile cellular 5G, or even satellite
Internet connections.

To meet this growth, the expanded content types and quality
expectations, and the expectation of any device, anywhere, over any
network, the streaming video technology infrastructure has had to evolve
significantly. This work is being done in the IETF and in the [Streaming
Video Technology Alliance (SVTA)](https://www.svta.org/), and in a
number of other technical and industry groups.

The growth of streaming video has contributed enormously to the growth
of the Internet. Internet connections at hundreds of megabits and
gigabit speeds have been driven in large part by the needs of video
streaming, as has the ongoing work on low-latency networking and
ultra-low-latency video delivery.

## Advances in Streaming Video Architecture

Internet streaming has greatly matured and diversified from its
early days of viewers watching pre-recorded standard definition
480p movies on wired PCs connected via high-latency, low-bandwidth
DSL or early DOCSIS modems.

Today, streaming platforms face significant technical challenges in
meeting viewer expectations:

1. Delivery scales that commonly range from hundreds of
   thousands to many millions of simultaneous viewers.
2. Low latency demands from live sports, live events, and live
   streamed content.
3. Content resolutions that have jumped from SD 480p to 4K
   (3840x2160) and 8K (7680x4320).
4. Devices with very diverse capabilities, from low-cost
   streaming sticks to Smart TVs, tablets, phones, and game
   consoles.
5. A broad range of connectivity choices including Wi-Fi,
   gigabit-speed low-latency DOCSIS, fiber, satellite, and 5G
   cellular networks.
6. Streaming formats such as MPEG-DASH and HLS, and transport
   protocols including HTTP/2 over TCP, HTTP/3 over QUIC, WebRTC,
   Media over QUIC (MoQ), and specialty transports such as Secure
   Reliable Transport (SRT) and the High Efficiency Streaming
   Protocol (HESP).

To meet these challenges, streaming platforms have significantly
invested in developing delivery architectures built on detailed
understanding of each element in the content delivery pathway,
from content capture all the way through to the viewer's screen.

Streaming applications are part of an end-to-end architecture
optimized around achieving the best experience including low
latency video delivery to viewing devices. The open Internet can
be unpredictable with temporary issues like packet loss,
congestion, and other conditions. However, streaming architecture
is designed to handle these momentary problems as effectively as
possible, often through dynamic adaptive approaches designed into
streaming protocols and platform components.

# Middleboxes and Learning from the Past

The IETF has discussed this situation in the past. More than
20 years ago, in 2002, Middleboxes: Taxonomy and Issues
{{?RFC3234}} was published, capturing the issues with middleboxes
in the network and the effects of hidden changes occurring on the
network between the sender and receiver.

Network overlays reverse the roles described there. With a
middlebox, the network changes traffic without the knowledge of
the endpoints. With a privacy-enhancing overlay, the endpoint's
traffic changes in ways the network and the content provider
cannot see. In both cases, one party has built its operations on
connection properties that another party changes or removes.

Many of the streaming impacts described in this document follow
this pattern. Client IP address, DNS resolver location, and
routing path are long-standing properties of Internet connections,
and CDN selection, geolocation, and troubleshooting are built on
them. When an overlay changes these properties without the
knowledge of the endpoints, those functions can break in ways
that are hard to detect or diagnose.

# Security Considerations

This document describes operational impacts and does not define
any protocol or mechanism. It introduces no new security
considerations of its own.

The network overlays discussed here are generally deployed to
improve user privacy and security. Nothing in this document
argues against their use.

Several of the impacts described in this document have security
implications for streaming platforms:

1. An overlay or proxy that terminates or downgrades an encrypted
   connection can invalidate end-to-end trust assumptions and
   expose content or user data to inspection or modification
   (see {{forced-encryption-downgrade}}).

2. Changes to the apparent client location can affect fraud
   detection and rights management processes that depend on
   consistent geographic identifiers (see
   {{geolocation-and-identification}}).

3. Security policies that expect viewers to connect from home or
   mobile networks can block legitimate users whose overlay
   egress traffic comes from data center or CDN address blocks
   (see {{unintended-content-blocking}}).

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge the contributions from the
Streaming Video Technology Alliance (SVTA) based on their work studying
the impacts of network overlays on streaming platforms. The
contributions from Brian Paxton on observed overlay behavior and
comments from Jay Robertson have been very helpful. The authors are also
very grateful to David Schinazi, Leonard Giuliano, Emile Stephan, and
Kyle Rose for their reviews and contributions to this document.

