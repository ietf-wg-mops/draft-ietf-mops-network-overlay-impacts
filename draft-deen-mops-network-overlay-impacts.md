---
title: "Network Overlay Impacts to Streaming Video"
abbrev: "NOISV"
category: info

docname: draft-deen-mops-network-overlay-impacts-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Media OPerationS"
keyword:
 - network overlay
 - MASQUE
 - streaming video
 - SVTA
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
    RFC2119:
    
informative:


--- abstract

This document examines the operational impacts that network overlays which introduce new alternative policies such as addressing, routing, DNS resolver changes to network connections have on streaming video delivery.    

--- middle

# Introduction


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

## Broken login and authentication 

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

TODO acknowledge.
