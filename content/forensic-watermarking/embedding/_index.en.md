---
title: "Watermark Embedding"
date: 2018-08-28T16:52:08+09:00
weight: 20
chapter: true
pre: "<b>2. </b>"
---

# Watermark Embedding

PallyCon Forensic Watermarking combines two versions of videos marked '0' and '1' in real-time at playback. It converts session information, such as user ID, to binary format, and thus composes the output stream by combining segments from the DASH or HLS content.

As a result, unique information about the playback session is inserted invisibly in the video and sent to the client.

```mermaid
sequenceDiagram
    participant A as End user
    participant B as Service site
    participant C as CDN
    participant D as PallyCon service
    A ->> B: Request URL for playback
    B ->> D: Content URL, Session data
    Note right of D: Session manager
    D -->> D: Generate session key(payload) and store session data
    D ->> B: Send Session URL
    B ->> A: Send Session URL
    A ->> C: Start playback of Session URL (request segments)
    Note right of C: Watermark embedder module
    C -->> C: Segments mixing by Session URL
    C ->> A: Send mixed segments
    Note right of A: Playback mixed content
```

> The segment list of the DASH manifest (.mpd) or HLS playlist (.m3u8) passed as a Session URL applies the same path and filename to all users, regardless of session information. Therefore, it is **safe from segment naming attack** which is mixing the segment lists of multiple sessions for the same content.

You can use the following documents to integrate the watermark embedder module with the session manager API.

<div class="cards">
<article class="card">
    <div class="text">
        <h3>Session Manager API Guide</h3>
        This document describes how to integrate the session URL for the watermark embedding through PallyCon session manager API.<p>
        <a href="{{<ref "session-manager.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
<article class="card">
    <div class="text">
        <h3>CloudFront CDN Embedder Module</h3>
        This document describes the Lambda@Edge embedder module that works with Amazon CloudFront CDN.<p>
        <a href="{{<ref "cloudfront-embedder.en.md">}}" target="_self" class="btn btn-default">Read More</a>
    </div>
</article>
</div>

***