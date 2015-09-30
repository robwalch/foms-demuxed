# foms-sf-2015
## FOMS notes

### Capabilities Detection Spec

- beyond canplay?


### MSE / EME

- Chromecast and Safari supports TS decryption (part of MediaSource)
  - but what's so good about TS? Can we just use fmp4 (fragmented mp4)?
- Capabilities of MSE today
  - Browser Unit Tests: http://w3c.github.io/test-results/media-source/all.html
  - JS Unit Tests for comparing Browser MSE implementation / Spec ambiguity (relaxing keyframes)
  - media metrics
- EME will be limitted to https
  - For cross-origin MSE jump on the proposal now!
  -   use fetch instead of xhr to get data...
- BufferZone Jerry!
  - a lot of talk about back buffer
    - showing it to users (quick seek back)
    - getting updates when it changes
    - what is the seekable range if duration is infinity (beyond buffered end-range)

### Feature Detection: Media Discovery Capability API
- Problem Space
  - 1. What's the best first quality (Network Connection API)
  - 2. Display constraints
    - HDR / 10-bit (tv)
  - 3. Codec?
- Any of this data could be misused from a privacy concern
  - our choices must be tempered with privacy in mind (fingerprinting , advertising based on this data) 
- max data rate
- frame rate
- multi-channel audio
- hardware codec support (does it matter? maybe for scaling to 4k (YT: currently using bad mime-type standards))
- on WiFi or mobile data (and likely a data limited plan)
- on Power or Battery
- Use WebGL to detect GPU capabiities (guessing whether it can be used for video)
  - get GPU name
- Multiple tag playback
- Memory pressure
- can cast/present (Presentation API)
- What else is going on in the bg (% of resources available)
- Media Session Spec
 - track attachment of media to decoder
 
### Apple
- File a radar bug with Apple for feature requests/bugs
  - file a radar: explain why your business needs it with data
  - https://bugreport.apple.com / https://developer.apple.com

### Youtube
- Requires MSE on TVs using YT. Make sure they expose it if you are developing for one of these device's dev tools
- 

### Chrome
- window.chrome has internals for debugging media errors
- Better "droppedFrames" reporting in 44-45
- How to reach chrome-media team?
  - https://crbug.com is your friend | label: Cr-Internals-Media
  - https://chromiumdev.slack.com | channel: #media

### DASH IF
- Has a collection of DASH test vectors (for members)
  - lists files mapped by use case
  - where it came from (who encoded it)
  - includes mp4box samples
  - include "error handling" samples
  - Vector Compliance Tool

### Shaka


### DASH
- Shaka uses a nice subset
- DASH.js is working on a modular refactor
- 
### SASH

### HLS

- LIVE / DVR - does it affect buffer ranges?
- Access video tracks to provide support for multi-audio after loaded metadata
  - Listen to track changes
  - seeking in live streams is there a seekable range in JavaScript?

### WebVTT

- http://dev.w3.org/html5/webvtt/
- WebVTT.js
- MediaRecorder API
- There is mapping from TTML to WebVTT

### Accessibility


### Codecs

- HDR video!?

- Browsers only have to fire timeupdate every 350ms
- Accessability: JW Player needs tabable Aria commented controls
- User Agent Accessability for keyboard controls
