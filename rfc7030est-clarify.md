---
title: "Clarification of Enrollment over Secure Transport (EST): transfer encodings and ASN.1"
abbrev: rfc7030est
docname: draft-richardson-lamps-rfc7030est-clarify-02

# stand_alone: true

ipr: trust200902
area: Internet
wg: LAMPS Working Group
kw: Internet-Draft
cat: std

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

- ins: W. Pan
  name: Wei Pan
  org: Huawei Technologies
  email: william.panwei@huawei.com

normative:
  RFC2119:
  RFC4648:
  RFC7030:
  I-D.ietf-anima-bootstrapping-keyinfra:

informative:
  RFC2616:
  RFC2045:
  RFC7230:
  RFC7231:
  errata4384:
    title: "EST errata 4384: ASN.1 encoding error"
    target: https://www.rfc-editor.org/errata/eid4384

  errata5107:
    title: "EST errata 5107: use Content-Transfer-Encoding"
    target: https://www.rfc-editor.org/errata/eid5107

  errata5108:
    title: "EST errata 5108: use of Content-Type for error message"
    target: https://www.rfc-editor.org/errata/eid5108


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

{{RFC7030}} defines the Enrollment over Secure Transport, or EST protocol.

This specification defines a number of HTTP end points for certificate enrollment and management.
The details of the transaction were defined in terms of MIME headers as defined in {{RFC2045}},
rather than in terms of the HTTP protocol as defined in {{RFC2616}} and {{RFC7230}}.

{{RFC2616}} and later {{RFC7231}} Appendix A.5 has text specifically
deprecating Content-Transfer-Encoding.

{{RFC7030}} calls it out this header incorrectly.

{{I-D.ietf-anima-bootstrapping-keyinfra}} extends {{RFC7030}}, adding new
functionality, and interop testing of the protocol has revealed that unusual processing
called out in {{RFC7030}} causes confusion.

EST is currently specified as part of IEC 62351, and is widely used in Government,
Utilities and Financial markets today.

Changes to {{RFC7030}} to bring it inline with typical HTTP processing would change
the on-wire protocol in a way that is not backwards compatible. Reports from the field
suggest that many implementations do not send the Content-Transfer-Encoding, and many
of them ignore it.

This document therefore revises {{RFC7030}} to reflect the field reality, deprecating
the extranous field.

This document deals with errata numbers {{errata4384}}, {{errata5107}}, and {{errata5108}}.

# Terminology

The abbreviation "CTE" is used to denote the Content-Transfer-Encoding header, and the abbreviation
"CTE-base64" is used to denote a request or response whose Content-Transfer-Encoding header contains
the value "base64".

# Requirements Language {#rfc2119}

In this document, the key words "MUST", "MUST NOT", "REQUIRED",
"SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" are to be interpreted as described in BCP 14, RFC 2119
{{RFC2119}} and indicate requirement levels for compliant STuPiD
implementations.

# Changes to EST endpoint processing

The {{RFC7030}} sections 4.1.3 (CA Certificates Response, /cacerts),
4.3.1/4.3.2 (Full CMC, /fullcmc), 4.4.2 (Server-Side Key Generation, /serverkeygen),
and 4.5.2 (CSR Attributes, /csrattrs) specify the use of base64 encoding with a
Content-Transsfer-Encoding for requests and response.

This document updates {{RFC7030}} to require the POST request and payload response of all
endpoints in to be {{RFC4648}} section 4 Base64 encoded DER.  This format is to be used
regardless of whether there is any Content-Transfer-Encoding header, and any value in that
header is to be ignored.

# Clarification of ASN.1 for Certificate Attribute set.

errata 4384.

# Clarification of error messages for certificate enrollment operations

errata 5108.

# Privacy Considerations

This document does not disclose any additional identifies to either active or
passive observer would see with {{RFC7030}}.

# Security Considerations

This document clarifies an existing security mechanism.  An option is
introduced to the security mechanism using an implicit negotiation.

# IANA Considerations

This document does not require any registrations.

# Acknowledgements

This work was supported by the Huawei Technologies.

--- back

