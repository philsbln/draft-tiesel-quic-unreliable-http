---
title: Unreliable Transmission Extension for HTTP/2 over QUIC
abbrev: Unreliable HTTP/2 over QUIC
docname: draft-tiesel-quic-unreliable-http-latest
date: 2017-08-11
category: info

ipr: trust200902
area: Transport
workgroup: QUIC Working Group
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: P. S. Tiesel
    name: Philipp S. Tiesel
    organization: Berlin Institute of Technology
    street: Marchstr. 23
    city: Berlin
    country: Germany
    email: philipp@inet.tu-berlin.de

normative:

informative:
  RFC2119:
  RFC7231:
  I-D.draft-ietf-quic-transport-04:
  I-D.draft-tiesel-unreliable-streams-00:
    title: "Considerations for Unreliable Streams in QUIC"
    date: 2017-08
    author:
      -
        name: Philipp S. Tiesel
        ins: P. S. Tiesel
    seriesinfo:
      ietf-tiesel-quic-unreliable-streams-00 (work in progress)
  I-D.draft-ietf-quic-http-04:

--- abstract

This draft outlines methods for requesting unreliable delivery
of HTTP response bodies over QUIC with unreliable streams [draft-tiesel-unreliable-streams].

--- middle

Conventions and Definitions
===========================

The words "MUST", "MUST NOT", "SHALL", "SHALL NOT", "SHOULD", and
"MAY" are used in this document. It's not shouting; when these
words are capitalized, they have a special meaning as defined
in {{RFC2119}}.



Introduction        {#intro}
============

HTTP has become part of application protocols used for time critical
applications like video streaming and back-office ad auctions.
These applications might have time constrains that can make
retransmissions of lost frames useless. 
Some of these applications may make use of partial delivery of
their messages, but reliable, in-order stream transmission blocks
the deliver of data after a gap in the stream by design.

This draft enables applications to request partial delivery of HTTP
objects by allowing to selectively disable retransmissions on HTTP 
responses.


General Concept
===============

This draft specifies a new HTTP header used to request unreliable
delivery of the HTTP request body. 
The server then uses unreliable QUIC streams as specified in
[draft-tiesel-unreliable-streams] to transfer HTTP request bodies
in a potentially unreliable way.
To prevent requests entering some kind of zombie state, headers
are always transferred reliably.


Requesting Unreliable transmission
==================================

By adding the following header, a HTTP client can request unreliable
transmission of the response body:

    Transport-Response-Reliability: unreliable

In case unreliable transmission should only be used to prevent
retransmissions after a certain deadline, the client hat add the following header:

    Transport-Response-Reliability: unreliable-after DATE

Where DATE uses the format specified in [RFC7231] with optionally
extending time-of-day to

   time-of-day = hour ":" minute ":" second
                | hour ":" minute ":" second ":" msec


Stream Mapping
==============

As the stream mapping between versions -04 and -05 of
[draft-ietf-quic-http] changes from separating
HTTP header and body into different QUIC streams into
having HTTP/2 frames of different types in one stream,
we need different stream mapping for these.

Note that draft-ietf-quic-http-04 allows much simpler
implementation as it does not require partial retransmission
within an unreliable stream.

Stream Mapping for draft-ietf-quic-http-04
------------------------------------------

Stream Mapping for draft-ietf-quic-http-05-wip
----------------------------------------------


Security Considerations {#sec}
=======================

TBD



IANA Considerations {#iana}
===================

TBD



--- back



