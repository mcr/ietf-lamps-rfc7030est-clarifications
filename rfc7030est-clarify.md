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
  email: thomas-werner@siemens.com

- ins: W. Pan
  name: Wei Pan
  org: Huawei Technologies
  email: william.panwei@huawei.com

normative:
  RFC2119:
  RFC2986:
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
Content-Transfer-Encoding for requests and response.

This document updates {{RFC7030}} to require the POST request and payload response of all
endpoints in to be {{RFC4648}} section 4 Base64 encoded DER.  This format is to be used
regardless of whether there is any Content-Transfer-Encoding header, and any value in that
header is to be ignored.

# Clarification of ASN.1 for Certificate Attribute set.

Section 4.5.2 of {{RFC7030}} is to be replaced with the following text:

## CSR Attributes Response

   If locally configured policy for an authenticated EST client
   indicates a CSR Attributes Response is to be provided, the server
   response MUST include an HTTP 200 response code.  An HTTP response
   code of 204 or 404 indicates that a CSR Attributes Response is not
   available.  Regardless of the response code, the EST server and CA
   MAY reject any subsequent enrollment requests for any reason, e.g.,
   incomplete CSR attributes in the request.

   Responses to attribute request messages MUST be encoded as the
   content-type of "application/csrattrs", and are to be "base64" {{RFC2045}}
   encoded.  The syntax for application/csrattrs body is as follows:

~~~~~~~~~~
{::include csrattr.asn1.txt}
~~~~~~~~~~
{: #csrmodule title="CSR Attributes Module"}

   An EST server includes zero or more OIDs or attributes {{RFC2986}} that
   it requests the client to use in the certification request.  The
   client MUST ignore any OID or attribute it does not recognize.  When
   the server encodes CSR Attributes as an empty SEQUENCE, it means that
   the server has no specific additional information it desires in a
   client certification request (this is functionally equivalent to an
   HTTP response code of 204 or 404).

   If the CA requires a particular crypto system or use of a particular
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
   crypto system (e.g., certification of a public key based on a certain
   elliptic curve) are represented as an attribute, to be reflected as
   the AlgorithmIdentifier of the SubjectPublicKeyInfo, with a type
   indicating the algorithm and the values indicating the particular
   parameters specific to the algorithm.  Requests for descriptive
   information from the client are made by an attribute, to be
   represented as Attributes of the CSR, with a type indicating the
   [RFC2985] extensionRequest and the values indicating the particular
   attributes desired to be included in the resulting certificate's
   extensions.

   The sequence is Distinguished Encoding Rules (DER) encoded [X.690]
   and then base64 encoded (Section 4 of [RFC4648]).  The resulting text
   forms the application/csrattr body, without headers.

   For example, if a CA requests a client to submit a certification
   request containing the challengePassword (indicating that linking of
   identity and POP information is requested; see Section 3.5), an
   extensionRequest with the Media Access Control (MAC) address
   ([RFC2307]) of the client, and to use the secp384r1 elliptic curve
   and to sign with the SHA384 hash function.  Then, it takes the
   following:

         OID:        challengePassword (1.2.840.113549.1.9.7)

         Attribute:  type = extensionRequest (1.2.840.113549.1.9.14)
                     value = macAddress (1.3.6.1.1.1.1.22)

         Attribute:  type = id-ecPublicKey (1.2.840.10045.2.1)
                     value = secp384r1 (1.3.132.0.34)

         OID:        ecdsaWithSHA384 (1.2.840.10045.4.3.3)

   and encodes them into an ASN.1 SEQUENCE to produce:

       30 41 06 09 2a 86 48 86 f7 0d 01 09 07 30 12 06 07 2a 86 48 ce 3d
       02 01 31 07 06 05 2b 81 04 00 22 30 16 06 09 2a 86 48 86 f7 0d 01
       09 0e 31 09 06 07 2b 06 01 01 01 01 16 06 08 2a 86 48 ce 3d 04 03
       03

   and then base64 encodes the resulting ASN.1 SEQUENCE to produce:

       MEEGCSqGSIb3DQEJBzASBgcqhkjOPQIBMQcGBSuBBAAiMBYGCSqGSIb3DQEJDjEJ
       BgcrBgEBAQEWBggqhkjOPQQDAw==

# Clarification of error messages for certificate enrollment operations

errata 5108.

# Privacy Considerations

This document does not disclose any additional identifies to either active or
passive observer would see with {{RFC7030}}.

# Security Considerations

This document clarifies an existing security mechanism.  An option is
introduced to the security mechanism using an implicit negotiation.

# IANA Considerations

The ASN.1 module in Appendix A of this doucment makes use of object
identifiers (OIDs).  This document requests that IANA register an
OID in the SMI Security for PKIX Arc in the Module identifiers
subarc (1.3.6.1.5.5.7.0) for the ASN.1 module.  The OID for the
Asymmetric Decryption Key Identifier (1.2.840.113549.1.9.16.2.54)
was previously defined in {{RFC7030}}.  IANA is requested to update
the "Reference" column for the Asymmetric Decryption Key Identifier
attribute to also include a reference to this doducment.

# Acknowledgements

This work was supported by the Huawei Technologies.

--- back
	
