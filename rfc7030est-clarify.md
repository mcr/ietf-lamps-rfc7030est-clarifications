---
title: "Clarification of Enrollment over Secure Transport (EST): transfer encodings and ASN.1"
abbrev: rfc7030est
docname: draft-ietf-lamps-rfc7030est-clarify-06

stand_alone: true

ipr: trust200902
area: Internet
wg: LAMPS Working Group
kw: Internet-Draft
cat: std
updates: 7030

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
  email: thomas-werner@siemens.com

- ins: W. Pan
  name: Wei Pan
  org: Huawei Technologies
  email: william.panwei@huawei.com

normative:
  RFC2119:
  RFC8179:
  RFC2986:
  RFC4648:
  RFC6268:
  RFC7030:
  X680:
    title: "Information technology - Abstract Syntax Notation One."
    date: 2002
    author:
      org: ITU-T
    seriesinfo:
      ISO/IEC: "8824-1:2002"
  X681:
    title: "Information technology - Abstract Syntax Notation One: Information Object Specification."
    date: 2002
    author:
      org: ITU-T
    seriesinfo:
      ISO/IEC: "8824-2:2002"
  X682:
    title: "Information technology - Abstract Syntax Notation One: Constraint Specification."
    date: 2002
    author:
      org: ITU-T
    seriesinfo:
      ISO/IEC: "8824-2:2002"
  X683:
    title: "Information technology - Abstract Syntax Notation One: Parameterization of ASN.1 Specifications."
    date: 2002
    author:
      org: ITU-T
    seriesinfo:
      ISO/IEC: "8824-2:2002"
  X690:
    title: "Information technology - ASN.1 encoding Rules: Specification of Basic Encoding Rules (BER), Canonical Encoding Rules (CER) and Distinguished Encoding Rules (DER)."
    date: 2002
    author:
      org: ITU-T
    seriesinfo:
      ISO/IEC: 8825-1:2002
  IEC62351:
    title: "Power systems management and associated information exchange - Data and communications security - Part 9: Cyber security key management for power system equipment"
    date: 2017
    author:
      org: International Electrotechnical Commission
    seriesinfo:
      ISO/IEC: 62351-9:2017
  errata4384:
    title: "EST errata 4384: ASN.1 encoding error"
    target: https://www.rfc-editor.org/errata/eid4384

  errata5107:
    title: "EST errata 5107: use Content-Transfer-Encoding"
    target: https://www.rfc-editor.org/errata/eid5107

  errata5108:
    title: "EST errata 5108: use of Content-Type for error message"
    target: https://www.rfc-editor.org/errata/eid5108

  errata5904:
    title: "EST errata 5904: use Content-Transfer-Encoding"
    target: https://www.rfc-editor.org/errata/eid5904

informative:
  I-D.ietf-anima-bootstrapping-keyinfra:
  RFC2616:
  RFC7230:
  RFC7231:


--- abstract

This document updates RFC7030: Enrollment over Secure Transport (EST) to resolve
some errata that was reported, and which has proven to cause interoperability
issues when RFC7030 was extended.

This document deprecates the specification of "Content-Transfer-Encoding"
headers for EST endpoints.
This document fixes some syntactical errors in ASN.1 that was presented.

--- middle

# Introduction

Enrollment over Secure Transport (EST) is defined in {{RFC7030}}.
The EST specification defines a number of HTTP end points for certificate enrollment and management.
The details of the transaction were defined in terms of MIME headers as defined in {{RFC2045}},
rather than in terms of the HTTP protocol as defined in {{RFC2616}} and {{RFC7230}}.

{{RFC2616}} and later {{RFC7231}} Appendix A.5 has text specifically
deprecating Content-Transfer-Encoding.
However, {{RFC7030}} incorrectly uses this header.

Any updates to {{RFC7030}} to bring it inline with HTTP processing risk
changing the on-wire protocol in a way that is not backwards compatible.
However, reports from implementers suggest that many implementations do not send the
Content-Transfer-Encoding, and many of them ignore it.
The consequence is that simply deprecating the header would remain compatible with current implementations.

{{I-D.ietf-anima-bootstrapping-keyinfra}} extends {{RFC7030}}, adding new
functionality, and interop testing of the protocol has revealed that unusual processing
called out in {{RFC7030}} causes confusion.

EST is currently specified as part of {{IEC62351}}, and is widely used in Government,
Utilities and Financial markets today.

This document therefore revises {{RFC7030}} to reflect the field reality, deprecating
the extraneous field.

This document deals with errata numbers {{errata4384}}, {{errata5107}}, {{errata5108}},
and {{errata5904}}.

This document deals explicitely with {{errata5107}} and {{errata5904}} in
{{estendpoint}}.
{{errata5108}} is dealt with in section {{error}}.

{{errata4384}} is closed by correcting the ASN.1 Module in {{csrasn1}}.

# Terminology

{::boilerplate bcp14}

# Changes to EST endpoint processing {#estendpoint}

The {{RFC7030}} sections 4.1.3 (CA Certificates Response, /cacerts),
4.3.1/4.3.2 (Full CMC, /fullcmc), 4.4.2 (Server-Side Key Generation, /serverkeygen),
and 4.5.2 (CSR Attributes, /csrattrs) specify the use of base64 encoding with a
Content-Transfer-Encoding for requests and response.

This document updates {{RFC7030}} to require the POST request and payload response of all endpoints use Base64 encoding as specified in Section 4 of {{RFC4648}}.
In both cases, the Distinguished Encoding Rules (DER) {{X690}} are used to produce the input for the Base64    encoding routine.
This format is to be used regardless of any Content-Transfer-Encoding header, and any value in such a header MUST be ignored.

## Whitespace processing

Note that "base64" as used in the HTTP {{RFC2616}} does not permit CRLF, while the "base64" used in MIME {{RFC2045}} does.
This specification clarifies that despite {{RFC2616}}, that white space including CR, LF, spaces (ASCII 32) and, tabs (ASCII 9) should be tolerated by receivers.
Senders are not required to insert any kind of white space.

# Clarification of ASN.1 for Certificate Attribute set. {#csrasn1}

Section 4.5.2 of {{RFC7030}} is to be replaced with the following text:

## CSR Attributes Response

If locally configured policy for an authenticated EST client indicates
a CSR Attributes Response is to be provided, the server response MUST
include an HTTP 200 response code.  An HTTP response code of 204 or 404
indicates that a CSR Attributes Response is not available.  Regardless
of the response code, the EST server and CA MAY reject any subsequent
enrollment requests for any reason, e.g., incomplete CSR attributes in
the request.

Responses to attribute request messages MUST be encoded as the
content-type of "application/csrattrs", and are to be "base64" {{!RFC2045}}
encoded.  The syntax for application/csrattrs body is as follows:

~~~
CsrAttrs ::= SEQUENCE SIZE (0..MAX) OF AttrOrOID

AttrOrOID ::= CHOICE {
  oid        OBJECT IDENTIFIER,
  attribute  Attribute {{AttrSet}} }

AttrSet ATTRIBUTE ::= { ... }
~~~

An EST server includes zero or more OIDs or attributes {{!RFC2986}} that
it requests the client to use in the certification request.  The client
MUST ignore any OID or attribute it does not recognize.  When the
server encodes CSR Attributes as an empty SEQUENCE, it means that the
server has no specific additional information it desires in a client
certification request (this is functionally equivalent to an HTTP
response code of 204 or 404).

If the CA requires a particular cryptographic algorithm or use of a particular
signature scheme (e.g., certification of a public key based on a
certain elliptic curve, or signing using a certain hash algorithm) it
MUST provide that information in the CSR Attribute Response.  If an
EST server requires the linking of identity and POP information (see
Section 3.5), it MUST include the challengePassword OID in the CSR
Attributes Response.

The structure of the CSR Attributes Response SHOULD, to the greatest
extent possible, reflect the structure of the CSR it is requesting.
Requests to use a particular signature scheme (e.g. using a
particular hash function) are represented as an OID to be reflected
in the SignatureAlgorithm of the CSR.  Requests to use a particular
cryptographic algorithm (e.g., certification of a public key based on a certain
elliptic curve) are represented as an attribute, to be reflected as
the AlgorithmIdentifier of the SubjectPublicKeyInfo, with a type
indicating the algorithm and the values indicating the particular
parameters specific to the algorithm.  Requests for descriptive
information from the client are made by an attribute, to be
represented as Attributes of the CSR, with a type indicating the
{{?RFC2985}} extensionRequest and the values indicating the particular
attributes desired to be included in the resulting certificate's
extensions.

The sequence is Distinguished Encoding Rules (DER) encoded {{X690}}
and then base64 encoded (Section 4 of {{!RFC4648}}).  The resulting text
forms the application/csrattr body, without headers.

For example, if a CA requests a client to submit a certification
request containing the challengePassword (indicating that linking of
identity and POP information is requested; see Section 3.5), an
extensionRequest with the Media Access Control (MAC) address
({{?RFC2307}}) of the client, and to use the secp384r1 elliptic curve
and to sign with the SHA384 hash function.  Then, it takes the
following:

~~~
      OID:        challengePassword (1.2.840.113549.1.9.7)

      Attribute:  type = extensionRequest (1.2.840.113549.1.9.14)
                  value = macAddress (1.3.6.1.1.1.1.22)

      Attribute:  type = id-ecPublicKey (1.2.840.10045.2.1)
                  value = secp384r1 (1.3.132.0.34)

      OID:        ecdsaWithSHA384 (1.2.840.10045.4.3.3)
~~~

and encodes them into an ASN.1 SEQUENCE to produce:

~~~
    30 41 06 09 2a 86 48 86 f7 0d 01 09 07 30 12 06 07 2a 86 48 ce 3d
    02 01 31 07 06 05 2b 81 04 00 22 30 16 06 09 2a 86 48 86 f7 0d 01
    09 0e 31 09 06 07 2b 06 01 01 01 01 16 06 08 2a 86 48 ce 3d 04 03
    03
~~~

and then base64 encodes the resulting ASN.1 SEQUENCE to produce:

~~~
    MEEGCSqGSIb3DQEJBzASBgcqhkjOPQIBMQcGBSuBBAAiMBYGCSqGSIb3DQEJDjEJ
    BgcrBgEBAQEWBggqhkjOPQQDAw==
~~~

# Clarification of error messages for certificate enrollment operations {#error}

{{errata5108}} clarifies what format the error messages are to be in.
Previously a client might be confused into believing that an error returned
with type text/plain was not intended to be an error.

## Updating section 4.2.3: Simple Enroll and Re-enroll Response

Replace:

~~~
    If the content-type is not set, the response data MUST be a
    plaintext human-readable error message containing explanatory
    information describing why the request was rejected (for
    example, indicating that CSR attributes are incomplete).
~~~

with:

~~~
    If the content-type is not set, the response data must be a
    plaintext human-readable error message containing explanatory
    information describing why the request was rejected (for
    example, indicating that CSR attributes are incomplete).
    Servers MAY use the "text/plain” content-type [RFC2046]
    for human-readable errors.
~~~

## Updating section 4.4.2: Server-Side Key Generation Response

Replace:

~~~
    If the content-type is not set, the response data MUST be a
    plaintext human-readable error message.
~~~

with:

~~~
    If the content-type is not set, the response data must be a
    plaintext human-readable error message.
    Servers MAY use the "text/plain” content-type [RFC2046]
    for human-readable errors.
~~~

# Privacy Considerations

This document does not disclose any additional identifies to either active or
passive observer would see with {{RFC7030}}.

# Security Considerations

This document clarifies an existing security mechanism.
It does not create any new protocol mechanism.

# IANA Considerations

The ASN.1 module in Appendix A of this document makes use of object
identifiers (OIDs).  This document requests that IANA register an
OID in the SMI Security for PKIX Arc in the Module identifiers
subarc (1.3.6.1.5.5.7.0) for the ASN.1 module.  The OID for the
Asymmetric Decryption Key Identifier (1.2.840.113549.1.9.16.2.54)
was previously defined in {{RFC7030}}.

IANA is requested to update the "Reference" column for the Asymmetric
Decryption Key Identifier
attribute to also include a reference to this document.

# Acknowledgements

This work was supported by Huawei Technologies.

The ASN.1 Module was assembled by Russ Housley and formatted by Sean Turner.
Russ Housley provided editorial review.

--- back

# ASN.1 Module

This annex provides the normative ASN.1 definitions for the structures
described in this specification using ASN.1 as defined in {{X680}},
{{X681}}, {{X682}} and {{X683}}.

The ASN.1 modules makes imports from the ASN.1 modules in
{{!RFC5212}} and {{!RFC6268}}.

There is no ASN.1 Module in RFC 7030.  This module has been created
by combining the lines that are contained in the document body.

~~~
{::include asn1-module.txt}
~~~



