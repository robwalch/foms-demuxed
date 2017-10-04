# foms-sf-2015
## FOMS notes

### JW Player Follow Up Steps
- Use seek and buffer (and played) ranges in html5 provider
  - support HLS DVR support in Safari
  - support buffer underflow
    - remove current timed stall detection
      - browsers only require a timeupdate event every 350ms
  - http://www.w3.org/html/wg/drafts/html/master/semantics.html#timeranges
  - https://github.com/heff/timeranges/blob/master/index.js 
- HLS.js considerations
  - fetch API / Streams
    - find a polyfill (xhr fallback)
      - We already have promises for handling stream input
      - Nicer xhr lib than our ajax util https://github.com/raynos/xhr
    - This will be a core part of superior performance in MSE streaming
  - Safari and Chromecast can decode TS files with MSE
    - canplay test?
    - Other browsers need to transcode MPEG-2 to fmp4
      - https://github.com/videojs/mux.js
- Accessibility concerns
  - WebVTT support https://github.com/mozilla/vtt.js
  - Descriptions (WebVTT descriptions / audio desctipions tracks or renditions for visually impared)
  - Aria "tooltips" for describing controls for screenreader
  - Internationalization
- Setup timeout occurs on bad network connection
  - Bad user experience on mobile networks or complex sites
  - Similar to Power Save error handling: a notification of the current status rather than a fatal error would be a big improvement
- IE8 support script
  - provide basic support via polyfills and display:table

### Capabilities Detection Spec
- beyond canplay?
- Network API (are we on a mobile network, wifi, no connection or unknown)
- list codec support
- framerate capabilities (60fps support)


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

### SASH
- Defined subset of DASH that uses json manifests
- https://github.com/sfvideo/sash
- contributors Phil Cluff and Jon Dajl of zencoder

### HLS

- LIVE / DVR - does it affect bufferand seek ranges in HTML5
  - HTML5 native support for features available in our Flash implementation may be possible
    - Support tracks and ranges
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

- HDR video 10-bit color channels for HDR capable displays
  - Not photo HDR or game HDR
  - Actual display technology that requires new codec support; VP9 Profile 2
  - UHD/4k 60fps base quality

- Visualizing codec motion vectors is cool
  - https://github.com/sfvideo/mplayer-debug

### HTML5
- Browsers only have to fire timeupdate every 350ms
- Accessability: JW Player needs tabable Aria commented controls
- User Agent Accessability for keyboard controls
