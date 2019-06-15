---
title: "Clarification of Enrollment over Secure Transport (EST): transfer encodings and ASN.1"
abbrev: rfc7030est
docname: draft-richardson-lamps-rfc7030est-clarifications-00

# stand_alone: true

ipr: trust200902
area: Internet
wg: 6tisch Working Group
kw: Internet-Draft
cat: info

coding: us-ascii
pi:    # can use array (if all yes) or hash here
  toc: yes
  sortrefs:   # defaults to yes
  symrefs: yes

author:

- ins: M. Richardson
  name: Michael Richardson
  org: Sandelman Software Works
  email: mcr+ietf@sandelman.ca

- ins: T. Werner
  name: Thomas Werner
  org: Siemens
  email: thomas.werner@siemens.com

normative:
  RFC2119:
  RFC7030:

informative:
  RFC2616:
  RFC7230:

--- abstract

This document updates RFC7030: Enrollment over Secure Transport (EST) to resolve
some errata that was reported, and which has proven to have interoperability
when RFC7030 has been extended.

This document deprecates the specification of "Content-Transfer-Encoding"
headers for EST endpoints, providing a way to do this in an upward compatible
way.  This document additional defines a GRASP discovery mechanism for EST
endpoints, and specifies requirements for them.

Finally, this document fixes some syntactical errors in ASN.1 that was
presented.

--- middle

# Introduction

# Terminology          {#Terminology}

# Requirements Language {#rfc2119}

In this document, the key words "MUST", "MUST NOT", "REQUIRED",
"SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" are to be interpreted as described in BCP 14, RFC 2119
{{RFC2119}} and indicate requirement levels for compliant STuPiD
implementations.

# Changes to EST endpoint processing

## Mitigation for existing clients

# Clarification of ASN.1 for Certificate Attribute set.

# Clarification of error messages for certificate enrollment operations

# Definition of GRASP discovery for updated EST servers

# Privacy Considerations

TBD.

# Security Considerations

TBD.

# IANA Considerations

TBD.

# Acknowledgements

This work was supported by the Huawei Technologies.

--- back

