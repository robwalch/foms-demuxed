- Media Capabilities
  Pintu - Google, Mozilla, Mux, Brightcove, Youtube, Netflix
  https://github.com/WICG/media-capabilities
  - video and audio capabilities
  - encyption capabilities
  - privacy issues
  - color depth, resolution and framerate decoding, vs display capabilities/transfer
  
- EME Next
  - Will EME stay with the W3C/WICG?
  - Common Media Application Format (CMAF) restriction on ISO-(fragmneted-mp4)
    - CMAF Akamai Blog Post https://blogs.akamai.com/2016/06/cmaf-what-it-is-and-why-it-may-change-your-ott-future.html
    - Only common encryption allowed
  - Test Suite for EME helping to drive V2
  - Communicate when display is restricted (no HDCP (VGA monitor) or Screen Sharing)
    - Should provide a key CDM error when output is restricted

- MSE Next
  - Buffer eviction is a problem because of the "quota exceeded" return from append
    - There currently isn't an eviction region notification when the buffer is cleared by the browser
  - We should be able to fill source buffers in the background and then switch out the buffer with the media element
  - Having 'labelAppend' to label quality of video buffered (bitrate, codec, width/height)
  - Access to internal buffered ranges for muxed source buffer (iso)
  - canplay/canplaythrough MSE events? haveCurrentData (one frame), haveFutureData (more than one Frame)
    - buffered TimeRange change tells you how far ahead of the playhead video is buffered
  - Fatal/Recovereable on MediaErrors with browser specific error messages (type of or reason for decode errors)
  - Make appendBuffer transactional rather than explode on error
  - Support playback through unbuffered ranges (conditionally)
  
- QoS / VideoQuality
  - https://wicg.github.io/media-playback-quality/
  - Is manual selection a signal that the adaptive quality is not good enough? Or that the communicated quality is lacking?
  - How much rebuffering?
  - Accurate time events and current time - (in background tab)?
  - Netflix looks at dropped frames in some browsers downswitch - using it an indication of low CPU
  - RequestAnimationFrame can be used as a proxy to measure rending speed (60hz or less?)
  - start time, buffering %, average bitrate played / played most
  
- Other Topics
  - Media Session (on mobile: what is playing and allow external to control of it) https://github.com/WICG/mediasession/blob/master/explainer.md
  - Live Captioning (Evol covering)
  - Decode Errors

- My interests
  - Fetch/Promises ReadableByteStream
    - The general consensus from Chrome and Mozilla is that fetch is buggy, and any streaming should be done via polyfill
  - Program date-time metadata/timecode (available in DASH - HLS not so easy - see "startTimeOffset"?)
  - Meet Joey
  - Talk shaka-player/dash.js

- Mozilla: We can't use the video tag with new `src` after using WideVine stream (SourceBuffer issues?)
  - write a web platform test
    - https://developer.mozilla.org/en-US/docs/Mozilla/QA/web-platform-tests
    - https://github.com/w3c/web-platform-tests

- Other
  - Viewability with Intersection Observer: https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API
  - Microsoft: get them to show up next week
  - WebVR: event next week
