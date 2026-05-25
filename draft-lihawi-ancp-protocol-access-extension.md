---
title: "Access Extensions for ANCP"
docname: draft-lihawi-ancp-protocol-access-extension-latest
updates: 6320
submissiontype: IETF
date: 2026-05-22
ipr: trust200902
category: exp
lang: en
v: 3

venue:
  github: "GIC-de/draft-lihawi-ancp-protocol-access-extension"

author:
-  fullname: "Christian Giese"
   organization: "RtBrick"
   email: "christian@rtbrick.com"
-  fullname: "Thomas Haag"
   organization: "Deutsche Telekom AG"
   email: "haagt@telekom.de"
-  fullname: "Birgit Witschurke"
   organization: "Deutsche Telekom AG"
   email: "b.witschurke@telekom.de"

informative:
   TR156:
      title: "Using GPON Access in the context of TR-101"
      author:
      - org: Broadband Forum
      date: 2012-11
      seriesinfo:
         TR: 156 Issue 3

--- abstract

This document specifies extensions to the Access Node Control Protocol (ANCP),
defined in RFC6320, to introduce support for Passive Optical Networks (PON)
as described in RFC6934, alongside evolving DSL technologies such as G.fast.
Furthermore, this document updates RFC6320 by modifying existing terminologies
and message flows, and by defining new Type-Length-Value (TLV) types to accommodate
these varied access technologies.

--- middle

# Introduction

{{!RFC6934}} introduces application of ANCP to PON. However, {{!RFC6320}}
has not been updated to support PON. In addition, DSL technology has continued
to evolve. G.fast, VDSL2 Vectoring and  VDSL2 Annex Q were introduced as upgraded
versions to provide higher bandwidths for the last mile.

This document considers all existing access technologies used in a
telco access network, yet not supported by {{!RFC6320}} and specifies
new TLVs accordingly.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This section repeats some definitions from {{!RFC6320}} and {{!RFC6934}},
but also updates some definitions where appropriate.

Access Node (AN):
: > Network device {{?RFC5851}}, usually located at a
    service provider central office or street cabinet that terminates access
    (local) loop connections from subscribers. In case the access loop is a
    Digital Subscriber Line (DSL), the Access Node provides DSL signal
    termination and is referred to as a DSL Access Multiplexer (DSLAM). In
    case the access loop is a Passive Optical Network (PON), the Access Node
    is referred to as an Optical Line Terminal (OLT).

Optical Line Terminal (OLT):
: > The OLT is located in the service provider's
    central office (CO) or street cabinet. It terminates and aggregates
    multiple PONs (providing fiber access to multiple premises or
    neighborhoods) on the subscriber side and interfaces with the Network
    Access Server (NAS) that provides subscriber management.

Optical Network Terminal (ONT):
: > The ONT terminates PON on the network side
    and provides PON adaptation. The subscriber side interface and the
    location of the ONT are dictated by the type of network deployment. For
    an FTTP deployment (with fiber all the way to the apartment or living
    unit), ONT has Ethernet (Fast Ethernet (FE) / Gigabit Ethernet (GE) /
    Multimedia over Coax Alliance (MoCA)) connectivity with the Home Gateway
    (HGW) / Customer Premises Equipment (CPE). In certain cases, one ONT may
    provide connections to more than one Home Gateway at the same time.

Optical Network Unit (ONU):
: > The ONU is a generic term denoting a device that
    terminates any one of the distributed (leaf) endpoints of an Optical
    Distribution Network (ODN), implements a PON protocol, and adapts PON
    PDUs to subscriber service interfaces. In the case of a multi-dwelling
    unit (MDU) or multi-tenant unit (MTU), a multi-subscriber ONU typically
    resides in the basement or a wiring closet (FTTB case) and has
    FE/GE/Ethernet over native Ethernet link or over xDSL (typically VDSL2)
    connectivity with each CPE at the subscriber premises. In the case where
    fiber is terminated outside the premises (neighborhood or curb side) on
    an ONT/ONU, the last-leg-premises connections could be via existing or
    new copper, with xDSL physical layer (typically VDSL2). In this case, the
    ONU effectively is a "PON-fed DSLAM". In new FTTdp based deployments the
    access node is named DPU (Distribution Point Unit). Basically from ANCP
    perspective this node provides the same functionality. Besides VDSL2,
    G.fast is mature and widely deployed.

# Modification to ANCP - General Aspects

ANCP message formats remain the same as described in
{{Section 3.5.1  of RFC6320}} when it is applied to PON.
However, some message descriptions need to be modified to
make them applicable to access networks other than
DSL deployments.

The ANCP Adjacency message is extended to other Access Technologies
than DSL. Generalize the message format to following:

The following capabilities are defined for ANCP:

- Capability Type: Access Topology Discovery = 0x01
  - Access technology: ANY
  - Length (in bytes): 0
  - Capability Data: NULL
  - For the detailed protocol specification of this capability,
    see {{Section 6 of RFC6320}}.

- Capability Type: Access Line Configuration = 0x02
  - Access technology: ANY
  - Length (in bytes): 0
  - Capability Data: NULL
  - For the detailed protocol specification of this capability,
    see {{Section 7 of RFC6320}}.

- Capability Type: Access Remote Line Connectivity Testing = 0x04
  - Access technology: ANY
  - Length (in bytes): 0
  - Capability Data: NULL
  - For the detailed protocol specification of this capability,
    see {{Section 8 of RFC6320}}.

# Modification to DSL-Type TLV 0x0091

Add the following new DSL-Type values.

Value: 32-bit unsigned integer

- G.fast = 8
- VDSL2 Annex Q = 9
- SDSL bonded = 10
- VDSL2 bonded = 11
- G.fast bonded = 12
- VDSL2 Annex Q bonded = 13

# Extension to DSL Sub TLV

DSL sub TLVs are listed in {{Section 6.5 of RFC6320}}.
G.fast requires beside existing TLVs the following new TLVs.

## Expected Throughput (ETR) TLV

- Type: 0x009B Expected Throughput at L2 (ETR) upstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the expected throughput upstream after
    retransmission (ITU-T G.997.2, clause 7.11.1.2).

- Type: 0x009C Expected Throughput at L2 (ETR) downstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the expected throughput downstream after
    retransmission (ITU-T G.997.2, clause 7.11.1.2).

## Attainable Expected Throughput (ATTETR) TLV

- Type: 0x009D Attainable Expected Throughput (ATTETR) upstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the attainable expected throughput upstream
    at L2 (ITU-T G.997.2, clause 7.11.2.2).

- Type: 0x009E Attainable Expected Throughput (ATTETR) downstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the attainable expected throughput downstream
    at L2 (ITU-T G.997.2, clause 7.11.2.2).


## Gamma Data Rate (GDR) TLV

- Type: 0x009F Gamma data rate (GDR) upstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the Gamma data rate (GDR)
  - upstream (ITU-T G.997.2, clause 7.11.1.3).

- Type: 0x00A0 Gamma Data Rate (GDR) downstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the Gamma data rate (GDR)
  - downstream (ITU-T G.997.2, clause 7.11.1.3).


## Attainable Gamma Data Rate (ATTGDR) TLV

- Type: 0x00A1 Attainable Gamma data rate (ATTGDR) upstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the attainable Gamma data rate
    upstream (ATTGDR) (ITU-T G.997.2, clause 7.11.2.3).

- Type: 0x00A2 Attainable Gamma data rate (ATTGDR) downstream
  - Length: 4 bytes
  - Value: Rate in kbits/s as a 32-bit unsigned integer.
  - Description: Reports the attainable Gamma data rate (ATTGDR)
    downstream (ITU-T G.997.2, clause 7.11.2.3).

# ANCP-Based PON Topology Discovery

This section describes topology discovery messages applied for PON.
TLVs not addressed here remain the same as applied for DSL.

## ANCP Port Up and Port Down Event Message Descriptions

The format of the ANCP Port Up and Port Down Event messages is
shown in {{updown}}. It has the same format as the one described in
section 6.3 of RFC6320. The only difference is that
DSL-Line-Attributes TLV is updated as Access-Line-Attributes TLV.

~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           TCP/IP Encapsulating Header (Section 3.2)           |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                ANCP General Message Header                    |
   +                      (Section 3.6.1)                          +
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~                    Unused (20 bytes)                          ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |x|x|x|x|x|x|x|x| Message Type  |   Tech Type   |  Reserved     |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |     # of TLVs                 | Extension Block length (bytes)|
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   ~                 Access line identifying TLV(s)                ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                ACCESS-Line-Attributes TLV                     |
   ~        (MANDATORY in Port Up, OPTIONAL in Port Down)          ~
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
{: #updown title="Format of the ANCP Port Up and Port Down Event Messages for PON Topology Discovery"}

NOTE: TLVs MAY be in a different order from what is shown in this figure.

See {{Section 3.6.1 of RFC6320}} for a description of the ANCP general
message header. The Message Type field MUST be set to 80 for Port Up,
81 for Port Down. It is applicable to both DSL and PON based access
systems. The 4-bit Result field MUST be set to zero (signifying Ignore).
The 12-bit Result Code field and the 24-bit Transaction Identifier field
MUST also be set to zeroes. Other fields in the general header MUST be
set as described in {{Section 3.6 of RFC6320}}.

The five-word Unused field is a historical leftover. The handling
of unused/reserved fields is described in {{Section 3.4 of RFC6320}}.

The remaining message fields belong to the "extension block", and
are described as follows:

Extension Flags (8 bits): The flag bits denoted by 'x' are
currently unspecified and reserved.

Message Type (8 bits): Message Type has the same value as in the
general header (i.e., 80 or 81).

Tech Type (8 bits): MUST be set to 0x01 (PON).

Reserved (8 bits): set as described in {{Section 3.4 of RFC6320}}.

\# of TLVs (16 bits): The number of TLVs that follow, not counting
TLVs encapsulated within other TLVs.

Extension Block Length (16 bits): The total length of the TLVs
carried in the extension block in bytes, including any padding within
individual TLVs.

TLVs: One or more TLVs to identify a PON Access line and zero or
more TLVs to define its characteristics.

## PON Access Line Identification

Most ANCP messages involve actions relating to a specific access line.
Thus, it is necessary to describe how PON access lines are identified
within those messages. This section defines four TLVs for that purpose
and provides an informative description of how they are used in PON.
TLVs not addressed here remain unchanged as applied for DSL.

### Access-Loop-Circuit-ID TLV

- Type: 0x0001
- Description: A locally administered human-readable string
  generated by or configured on the Access Node, uniquely identifying
  the corresponding access loop logical port on the user side of the
  Access Node, as described in Section 5.7 of {{TR156}}.
- Length: Up to 63 bytes
- Value: ASCII string

### Access-Loop-Remote-ID TLV

- Type: 0x0002
- Description: An operator-configured string that uniquely
  identifies the user on the associated access line, as
  described in Section 5.7 of {{TR156}}.
- Length: Up to 63 bytes
- Value: ASCII string

### PON-Access-Line-Attributes TLV

- Type: 0x0012
- Description: This TLV encapsulates attribute values of a PON
  access line serving a subscriber.
- Length: Variable (up to 1023 bytes)
- Value: One or more encapsulated TLVs corresponding to PON access
  line attributes. The PON-Access-Line-Attributes TLV MUST contain at
  least one TLV when it is present in a Port Up or Port Down message.
  The actual contents are determined by the AN control
  application. Technology-independent attributes of {{!RFC6320}},
  such as TLV0x0090, are valid for PON and not repeated here.

### PON-Access-Type TLV

- Type: 0x0097
- Description: Indicates the type of PON transmission system in use.
- Length: 4 bytes
- Value: 32-bit unsigned integer
  - OTHER = 0
  - GPON = 1
  - XG-PON1 = 2
  - TWDM-PON = 3
  - XGS-PON = 4
  - WDM-PON = 5
  - Unknown = 7
  - 25GS-PON = 8
  - 50G-PON = 9

### ONT/ONU-Average-Data-Rate-Downstream TLV

- Type: 0x00B0
- Description: ONT/ONU downstream average data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### ONT/ONU-Peak-Data-Rate-Downstream TLV

- Type: 0x00B1
- Description: ONT/ONU downstream peak data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### ONT/ONU-Maximum-Data-Rate-Upstream TLV

- Type: 0x00B2
- Description: ONT/ONU upstream maximum data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### ONT/ONU-Assured-Data-Rate-Upstream TLV

- Type: 0x00B3
- Description: ONT/ONU upstream assured data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### PON-Tree-Maximum-Data-Rate-Upstream TLV

- Type: 0x00B4
- Description: PON Tree upstream maximum data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### PON-Tree-Maximum-Data-Rate-Downstream TLV

- Type: 0x00B5
- Description: PON Tree downstream maximum data rate L2
- Length: 4 bytes
- Value: Rate in kbits/s as a 32-bit unsigned integer

### Reserved TLV

- Type: 0x00B6 - 0x00B7
- Description: Reserved
- Length: tbd
- Value: tbd

# Security Considerations {#security}

This document does not introduce additional security considerations
beyond those discussed in {{!RFC6320}} and {{!RFC6934}}.

# IANA Considerations

## Early IANA Allocation Request

**Note to the RFC Editor and IANA:**
Multiple vendors have already implemented the ANCP extensions described in this document,
and the exact TLV Type values specified below are actively deployed in production networks.
To prevent severe breakage of backwards compatibility within this existing deployed base,
the authors formally request the early allocation of these specific TLV Type values
according to the procedures defined in {{!RFC7120}}.

## ANCP TLV Type Registry

This document defines the following sets of new TLVs for PON and evolving DSL access technologies.
Some values previously defined by {{!RFC6320}} are referenced here for completeness.

IANA is requested to allocate the specific code points listed below in the "ANCP TLV Type" registry.

| Type Code | TLV Name                                               | Reference |
| --------- | ------------------------------------------------------ | --------- |
| 0x0000    | Reserved                                               | RFC 6320  |
| 0x0001    | Access-Loop-Circuit-ID                                 | RFC 6320  |
| 0x0002    | Access-Loop-Remote-ID                                  | RFC 6320  |
| 0x0003    | Access-Aggregation-Circuit-ID-ASCII                    | RFC 6320  |
| 0x0005    | Service-Profile-Name                                   | RFC 6320  |
| 0x0006    | Access-Aggregation-Circuit-ID-Binary                   | RFC 6320  |
| 0x0011    | Command                                                | RFC 6320  |
| 0x0012    | PON-Access-Line-Attributes                             | RFC TBD1  |
| 0x0097    | PON-Access-Type                                        | RFC TBD1  |
| 0x0098    | Reserved                                               | RFC TBD1  |
| 0x0099    | Reserved                                               | RFC TBD1  |
| 0x009A    | Reserved                                               | RFC TBD1  |
| 0x009B    | Expected Throughput (ETR) upstream                     | RFC TBD1  |
| 0x009C    | Expected Throughput (ETR) downstream                   | RFC TBD1  |
| 0x009D    | Attainable Expected Throughput (ATTETR) upstream       | RFC TBD1  |
| 0x009E    | Attainable Expected Throughput (ATTETR) downstream     | RFC TBD1  |
| 0x009F    | Gamma Data Rate (GDR) upstream                         | RFC TBD1  |
| 0x00A0    | Gamma Data Rate (GDR) downstream                       | RFC TBD1  |
| 0x00A1    | Attainable Gamma Data Rate (ATTGDR) upstream           | RFC TBD1  |
| 0x00A2    | Attainable Gamma Data Rate (ATTGDR) downstream         | RFC TBD1  |
| 0x00B0    | ONT/ONU-Average-Data-Rate-Downstream                   | RFC TBD1  |
| 0x00B1    | ONT/ONU-Peak-Data-Rate-Downstream                      | RFC TBD1  |
| 0x00B2    | ONT/ONU-Maximum-Data-Rate-Upstream                     | RFC TBD1  |
| 0x00B3    | ONT/ONU-Assured-Data-Rate-Upstream                     | RFC TBD1  |
| 0x00B4    | PON-Tree-Maximum-Data-Rate-Upstream                    | RFC TBD1  |
| 0x00B5    | PON-Tree-Maximum-Data-Rate-Downstream                  | RFC TBD1  |
| 0x00B6    | Reserved                                               | RFC TBD1  |
| 0x00B7    | Reserved                                               | RFC TBD1  |
| 0x0106    | Status-Info                                            | RFC 6320  |
| 0x1000    | Target (single access line variant)                    | RFC 6320  |
| 0x1001 -  | Reserved for Target variants                           | RFC 6320  |

*Note to RFC Editor: Please replace "TBD1" with the assigned RFC number prior to publication.*

## Creation of the ANCP DSL-Type Registry

This document requests that IANA create a new registry titled "ANCP DSL-Type" within the
Access Node Control Protocol (ANCP).

The registration procedure for this registry is "IETF Review" as defined in RFC 8126.

The registry consists of 32-bit unsigned integers. IANA is requested to populate this new registry with the initial values defined in RFC 6320, as well as the new values defined in this document (which are subject to the early allocation request noted above).

| Value | Description            | Reference     |
| ----- | ---------------------- | ------------- |
| 1     | ADSL1                  | RFC 6320      |
| 2     | ADSL2                  | RFC 6320      |
| 3     | ADSL2+                 | RFC 6320      |
| 4     | VDSL1                  | RFC 6320      |
| 5     | VDSL2                  | RFC 6320      |
| 6     | SDSL                   | RFC 6320      |
| 7     | Unknown                | RFC 6320      |
| 8     | G.fast                 | TBD1          |
| 9     | VDSL2 Annex Q          | TBD1          |
| 10    | SDSL bonded            | TBD1          |
| 11    | VDSL2 bonded           | TBD1          |
| 12    | G.fast bonded          | TBD1          |
| 13    | VDSL2 Annex Q bonded   | TBD1          |

## Creation of the ANCP PON-Access-Type Registry

This document requests that IANA create a new registry titled "ANCP PON-Access-Type"
within the Access Node Control Protocol (ANCP) parameters.

The registration procedure for this registry is "IETF Review" as defined in RFC 8126.

The registry consists of 32-bit unsigned integers representing the type of PON transmission system in use. IANA is requested to populate this new registry with the initial values defined in this document (which are subject to the early allocation request noted above).

| Value | Description            | Reference     |
| ----- | ---------------------- | ------------- |
| 0     | OTHER                  | TBD1          |
| 1     | GPON                   | TBD1          |
| 2     | XG-PON1                | TBD1          |
| 3     | TWDM-PON               | TBD1          |
| 4     | XGS-PON                | TBD1          |
| 5     | WDM-PON                | TBD1          |
| 7     | Unknown                | TBD1          |
| 8     | 25GS-PON               | TBD1          |
| 9     | 50G-PON                | TBD1          |

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to give special recognition to Hongyu Li, who served as a primary
author on earlier versions of this draft. His foundational efforts and significant technical
contributions during his time at Huawei Technologies were instrumental in shaping this
document.

Many thanks to Norbert Voigt, John Gibbons, Sven Ooghe, Koen De Sagher and
Sven Leimer for joint work reviewing the document and providing valuable
comments to this document.
