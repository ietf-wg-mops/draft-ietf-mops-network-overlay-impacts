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

This document examines the operational impacts to streaming video applications caused by changes to network policies by network overlays.  The network policy changes incluide a wide range including changes to address assignment, transport protocols, routing, DNS resolver which in turn affect a variety of important content delivery aspects such as latency, CDN cache selection, delivery path choices, traffic classification and content access controls.

--- middle

# Introduction

Internet streaming has greatly matured and diversified from its early days of viewers watching pre-recorded 320x240, 640x480 standard definiton 480p movies to wired PCs connected to the Internet via high-latency, low-bandwidth DSL as early DOCSIS modems.  

Streaming has grown be a daily go-to video source world wide for billions of viewers and has expanded from pre-recorded movies to encompass every type of video imaginable.   Viewers now steam latency sensitive live sports, live events and contents, content  resolutions and corresponding formats have jumped from SD-480p to 4K (3840x2160) and 8K (7680x4320) along with bit rates which can had data needs of 10-24+ Mbps for 4K with 8K demanding 40 Mbps under extreme compression and 150-300 Mbps for high quality such as cinema, to a broard range of devices from cheap stream sticks, to Smart TVs, tablets, phones, and game consoles, connecting to streaming sources over a deverse set of networking technologies including WiFi, gig speed-low latency DOCSIS, satellite, and 5G cellular networks, using a growing list of application transport protocols which now include DASH, HLS, http2/tcp, http3/QUIC, WebRTC, SRT, HESP, and soon Media over QUIC (MoQ)



Streaming video applications and streaming delivery operations are increasingly optimized to the network and content delivery environment that operate in.  As Internet streaming has grown matured as a video delivery pathway 

Streaming video services are the fastest growing application running on the Internet.   The streaming of pre-recorded content such as movies, TV shows and video series since its commercially viable emergence in the late 2000s has become both a global way of accessing content and has become the leading source of Internet traffic.    With acceptance of streaming as a video delivery channel, live events such as news , concerts and sporting events have begun appearing on streaming services.  Internet Streaming of recorded and live content has come of age globably viewers globally over the may different paths - broadband, mobile-wireless and satellite.

Streaming's subscriber growth, resolution and quality jumps from SD to HD to 4K and even 8K, and ever smaller latency goals have created a number of scaling, operational economics, and techinical challenges that have required innovation in both video technology - formats, encoding, players - and in network technology - transports, caching, routing, DNS - to keep up with viewer demands.

At the same time the streaming industry has been busy developing and deploying technologies like SVTA OpenCaching, the Internet's own architecture has been undergoing fundamental changes.  While VPNs have long been a security tool to protect corporate connections from users connecting to their work networks, newer protocols like MASQUE and commercial services built upon them have began being widely used by individuals for general Internet access.

Historically VPN users rarely accessed streaming services over VPNs, largely for performance reasons since in the past many VPNs did not have the bandwidth to support high quality video, and more recently becuase many streaming services have chosen to block use from VPNs due to their extensive use to bypass regional content distribution restrictions.    The result has been that VPNs have largely not had an operational impact to streaming platforms.

More recently, new privacy focused transports such as MASQUE based http2/http3 protocol specific network overlay technologies have become available to consumers who and these network overlays in turn have become part of the delivery pipeline between their adopters devices and the video streaming platform service infrastructures such as CDN caches, authentication and UI servers and platform security and protection services.

# Impacts

## Improper Cache Selection

## Alternate Routing and Unexpected Routing

## Bypass of Native Network Policies

## Incorrect Traffic Classification

## Increased Latency

## Impacts of Altered IP Address

## Altered logging

## Geofilters breakage

### unintended blocking

## Broken login/authentication

# Ideas to Mitigate Impacts

## Application notification of policy change

## Interfaces to access policy settings

## Enable Application Level Opt IN or Opt OUT


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
