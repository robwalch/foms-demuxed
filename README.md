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
    - Performance API (resoures, first paint, long task observers)
    - Video playback quality
- WebVTT / IMSC1
- * MetaData (SCTE, DATERANGE, Cross-Browser)
    - Frame accurate
- Error Handling
    - Specific segment (or url)

### Lightning Talk: Will Law Common-Media-Client-Data
- CTE spec for Player to tell CDN what it needs and how it's doing
- TODO: get pics (urls) from phone


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

Eric 



###

Notes measure buffering length(s) at canplay across UAs