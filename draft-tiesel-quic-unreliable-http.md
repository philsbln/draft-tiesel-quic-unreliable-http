---
title: Unreliable Transmission Extension for HTTP/2 over QUIC
abbrev: Unreliable HTTP/2 over QUIC
docname: draft-tiesel-quic-unreliable-http-latest
date: 2017-09-05
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
  RFC7231:
  RFC7540:

informative:
  RFC2119:
  I-D.ietf-quic-transport:
  I-D.tiesel-quic-unreliable-streams:
  I-D.ietf-quic-http:

--- abstract

This draft outlines methods for requesting unreliable delivery
of HTTP response bodies over QUIC with unreliable streams specified in {{I-D.tiesel-quic-unreliable-streams}}.

--- middle

Conventions and Definitions
===========================

The words "MUST", "MUST NOT", "SHALL", "SHALL NOT", "SHOULD", and
"MAY" are used in this document. It's not shouting; when these
words are capitalized, they have a special meaning as defined
in {{RFC2119}}.



Introduction        {#intro}
============

HTTP has become part of application protocols used for time sensitive applications such as video streaming and back-office ad auctions.
These applications might have time constraints that can make retransmissions of lost frames useless.
Some of these applications can operate on partially delivered messages, but waiting for retransmissions blocks the delivery of data after a gap in the stream by design.

This draft enables applications to request partial delivery of HTTP objects by allowing to disable retransmissions for HTTP response bodies.


General Concept
===============

This draft specifies a new HTTP header for requesting unreliable delivery of the HTTP request body.
For answering requests including this header, the server  uses unreliable QUIC streams as specified in
{{I-D.tiesel-quic-unreliable-streams}} to transfer HTTP request bodies in a (partially) unreliable way.
To use the regular HTTP client logic, headers
are always transferred reliably.


Requesting Unreliable transmission
==================================

By adding the following header, an HTTP client can request unreliable transmission of the response body

    Transport-Response-Reliability: unreliable

In case unreliable transmission should only be used to prevent
retransmissions after a certain deadline, the client hat add the following header

    Transport-Response-Reliability: unreliable-after DATE

Where DATE is either a relative offset in milliseconds or a date as specified in [RFC7231] with optionally extending time-of-day to:

    time-of-day = hour ":" minute ":" second
                | hour ":" minute ":" second "." msec


In case of  having requested unreliable delivery with
the ```unreliable-after``` verb, retransmissions on that stream should be stopped after the time specified.

For unreliable deliver with using the ```unreliable``` verb, the server may use domain knowledge about the data transmitted to decide whether to retransmit parts of the data.



Stream Mapping
==============

The stream mapping scheme changes between versions -04 and -05 of
{{I-D.ietf-quic-http}}. While version -04 separates
HTTP header and body into different QUIC streams,
version -05 transports multiple HTTP/2 frames of different types within one stream.
We present different stream mapping for these versions.

Note that draft-ietf-quic-http-04 allows a simpler
implementation as it does not require partial retransmission within an unreliable stream.

Stream Mapping for draft-ietf-quic-http-04
------------------------------------------

The control stream MUST alway use a reliable stream to ease state keeping.

When indicated by the ```Transport-Response-Reliability``` HTTP header,
the server SHOULD open the data stream as unreliable stream.


Stream Mapping for draft-ietf-quic-http-05
------------------------------------------

As a prerequisite to requesting unreliable delivery of HTTP objects, the client MUST open a stream used for the request as an unreliable stream.
The ```Transport-Response-Reliability``` HTTP header sent over reliable streams SHOULD be ignored.

Despite opening the stream as an unreliable stream, all HTTP/QUIC frame headers, as well as the payload of ```HEADERS``` frames, MUST be transmitted reliably to re-use normal HTTP/2 application logic.


Security Considerations {#sec}
=======================

TBD



IANA Considerations {#iana}
===================

TBD



--- back



