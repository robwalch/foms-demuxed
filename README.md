# FOMS [SF] October 2019

## Monday 11/21

### Position Statement
Rob Walch - JW Player, Web Player Principal

- Low-Latency
    - MSE v2 (codec transition)
    - Bandwidth Estimation (+ABR)
    - fetch in worker (which browser)
    - LL Workflow
        - fetch/xhr issue: H/2 byteranges never cached
- Player Benchmarking
    - Performance Resource Timing (resoures, first paint, long task observers)
    - Video playback quality
- WebVTT / IMSC1
- * MetaData (SCTE, DATERANGE, Cross-Browser)
    - Frame accurate
- Error Handling
    - Specific segment (or url)

### Lightning Talk: Will Law Common-Media-Client-Data

- CTE spec for Player to tell CDN what it needs and how it's doing
- TODO: get pics (urls) from phone

    *Coming soon: Common-Media-Server-Data

### MSE v2

Wolz:
- Main features (not yet available)
    - MSE available in worker context
        - ObjectUrl context creation in the Worker (pass it to main thread)
        - need buffered and currentTime from BufferSource and MediaElement in main thread
    - Chrome Origin trial this/next quarter
    - Firefox / Safari to follow
- API for standardizing interop around gaps
    - https://github.com/w3c/media-source/issues/160
    - allow gaps? on which tracks?
        - minimum jump
        - play-through
    - pops with audio gaps
    - Tell SourceBuffer how to deal with gaps (configure buffer)

v-Next:
- Appending small amounts of data (frames)
    - Chrome does 128k at a time
    - Scheduling overhead
    - Does byte-stream format have to be one mp4
    - GOP based? Frame based
    - Min playback buffer to player "canplay"?
        - ~3 frames, 200ms audio in Chrome
        - 2-3 seconds
        - ~30 frame latency in Edge
- Chrome latency hint
    - Byte stream can say it's live to just play
- Minimum playback buffer
    - See Exo play when ready (load control determines "ready" or "canplay" / have future data or autoplay has enough)
    - Shaka sets playbackRate to 0 to wait for more data
- Auto EMSG data extraction
    - using box parser sucks
    - Add a SourceBuffer event please, sidx time offset
    - Safari does it for ID3 emsg data
- Other topics we didn't get to
    - Infinate GOP GC
    - Multi SourceBuffer
    - new SourceBuffer no tag
    
### Talk: Eric @ Apple

- `TextTrackCue` constructor that takes document fragment (VTT markup?)
    - fragment can have inline styles
- Native rendering honors Apple accessibility subtitle styling preferences

### Talk: MediaCapabilities updates

- HLS decode and display support
- Spacial Audio support

### Low-Latency

- WebTransport
    - Reliable vs Unreliable data channel (ex: http vs udp)
    - Quic / WebSocket / UDP / WebRTC 
    - Application can fallback to HTTP2 or Quic fallback on it's own
    - WebRTC is overkill for Client-Server data transfer (more for P2P)
    - Unreliable Quic provides congestion control not available with UDP alone
    
- Common Workflow
    - How to simultaneously publish
        - Dash-LL (CTE)
        - LHLS (CTE)
        - LL-HLS from Apple
            - Will: double egress, less cache efficiency
            - CDNs don't do range-requests for partial files
            - Browser cache doesn't support H/2 push range-requests
                - Link header is being reused which breaks other uses
                - Will: make a new header explicitly for this
            - Steve: Who will implement this spec?
            
- Bandwidth Estimation
    - ACTE doesn't work (for Twitch in browsers outside of Chrome)
    - Side load to get estimation
        - more packets in addition to current or another rendition?
    - A new API for getting bandwidth (server-client) from the browser
        - Will: extension to fetch API? kbps
        - NetworkInfo?
        - Performance Resource Timing?
    - BYO Readable Stream in fetch or set highwatermark
    - Measure bursts
        - but what about web developers who use dev tools to throttle and complain about player behavior
        - small chunks may always be offloaded and come bursts that look like higher throughput than what is available
        - H/2 cache ruins estimation
    - Hit up WHATWG for fetch extensions
        
- PlaybackRate adjustment
    - Pitch correction is not uniform

- WebRTC
    - Will: When will AV1 real-time be supported (Using "David" decoder)

### Shaka Streamer

- Simply setup packaging for streaming without all the complexity involved in using FFMPEG
- widevine
- (Google) cloud push

### Media-Internals

It's coming to Dev-Tools!

### Error Handling

- Video decode errors (code 3)

- Microsoft extended error codes on media-errors
    - Two pages of DRM errors
- Chrome EME DRM errors

- Eric: Don't worry about what's possible or what the underlying errors are
    - Identify the type of information that would be useful and make a proposal
    
### Cross-platform, interop

Interop issues
- HLS Manifest metadata - patch coming to Safari Preview
- MSE Buffer eviction
    - New eviction policy proposal (in MSE v2)
- setLiveSeekableRange needed when using native controls
- Libraries for testing support on TVs
    - https://www.cobalt.dev/
    - https://github.com/cta-wave/WMAS
- EME crypto scheme detection
- fetch in workers is well supported in recent Chrome
    - Not in Safari
    - Firefox?
    - TVs... nah
- Only naive fullscreen on iOS
- no more 360 video please

### Notes 

- When do browsers start playback? Measure buffering length(s) at canplay across UAs
- Eric @ Apple has a patch for inband manifest DateRange metadata
- ArrayBufferBuffer spec
    (Iterable of ArrayBuffers that acts as a single ArrayBuffer to avoid concats/alloc)
