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
  RFC7231:
  RFC7540:

informative:
  RFC2119:
  I-D.ietf-quic-transport:
  I-D.tiesel-unreliable-streams:
    title: "Considerations for Unreliable Streams in QUIC"
    date: 2017-08
    author:
      -
        name: Philipp S. Tiesel
        ins: P. S. Tiesel
    seriesinfo:
      ietf-tiesel-quic-unreliable-streams (work in progress)
  I-D.ietf-quic-http:

--- abstract

This draft outlines methods for requesting unreliable delivery
of HTTP response bodies over QUIC with unreliable streams {{I-D.tiesel-unreliable-streams}}.

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
{{I-D.tiesel-unreliable-streams}} to transfer HTTP request bodies
in a potentially unreliable way.
To prevent requests entering some kind of zombie state, headers
are always transferred reliably.


Requesting Unreliable transmission
==================================

By adding the following header, a HTTP client can request unreliable
transmission of the response body:

    Transport-Response-Reliability: unreliable

In case unreliable transmission should only be used to prevent
retransmissions after a certain deadline, the client hat add the
following header:

    Transport-Response-Reliability: unreliable-after DATE

Where DATE is either an relative offset in milliseconds or uses
the format specified in [RFC7231] with optionally extending
time-of-day to

   time-of-day = hour ":" minute ":" second
                | hour ":" minute ":" second "." msec


In case of requested having requested unreliable deliver with using
the ```unreliable-after``` verb, retransmissions on that stream
should be stopped after the time specified.

For unreliable deliver with using the ```unreliable``` verb, the
server may use domain knowledge about the data transmitted to decide
whether to retransmit parts of the data.



Stream Mapping
==============

As the stream mapping between versions -04 and -05 of
{{I-D.ietf-quic-http}} changes from separating
HTTP header and body into different QUIC streams into
having HTTP/2 frames of different types in one stream,
we need different stream mapping for these.

Note that draft-ietf-quic-http-04 allows much simpler
implementation as it does not require partial retransmission
within an unreliable stream.

Stream Mapping for draft-ietf-quic-http-04
------------------------------------------

The control stream MUST alway use a reliable stream to ease state keeping.

When indicated by the ```Transport-Response-Reliability``` HTTP header,
the server SHOULD open the data stream as unreliable stream.


Stream Mapping for draft-ietf-quic-http-05-wip
----------------------------------------------

As prerequisite to requesting unreliable delivery of HTTP objects,
the client MUST open the new stream as unreliable stream.
The ```Transport-Response-Reliability``` HTTP header sent over reliable
streams SHOULD be ignored.

Despite opening the stream as unreliable stream, all HTTP/QUIC frame
headers as well as the payload of ```HEADERS``` frames MUST be
transmitted reliably to ease state keeping.


Security Considerations {#sec}
=======================

TBD



IANA Considerations {#iana}
===================

TBD



--- back



