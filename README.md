# FOMS/Demuxed San Francisco 2017

## FOMS notes

https://public.etherpad-mozilla.org/p/FOMS-2017

## 10/03/2017

### Autoplay Policy

https://public.etherpad-mozilla.org/p/FOMS-2017-autoplay

- Reasons a play promise is rejected
  1. user gesture required
  2. load called
  3. pause called

- Promise rejection error stack trace ?

- Result of play promise rejected
  - video.paused = true
  - Safari fires video events 'playing' then 'paused' ensure we go into a paused state
  - Chrome does not (appearently we were always paused, but don't count on paused state)

- Bad states
  - preload impacts this behavior?

- Priming the video tag
  - .load() on window.event
  - .src = / .srcObject
  - .play() -> video.paused = false (OK in older iOS - don't count on it in Chrome)

- How do we determine if we're in a user gesture event when api.play() is called?

- Can we get this requirement before trying?
  - canplay(type)
  - canAutoplay(NOW?) -> "maybe"

- disable audio tracks instead of mute = true to turn off audio decoders

#### iOS
- muted autoplay allowed
- consent for audio on a page
- one video tag at a time
- What if you unmuted programmatically (pauses)

#### Safari 11
- Per frame (not video element)
- no iFrame policy
- configurable per domain (autoplay and autoplay muted)
- autoplay=true muted video pauses offscreen (not video started by calling .play())

#### Chrome 64
- muted autoplay allowed (even in iFrame)
- click required on origin (tab session)
- 7 seconds with video and audio larger than a certain size is meaningful media interaction (Desktop)
- Android - adding page to homescreen
- iFrame inherits page policy -> special attribute to pass to iFrame "gesture=media"
- What if you unmuted programmatically at 7 seconds

### EME

https://public.etherpad-mozilla.org/p/FOMS-2017-eme

- Encryption scheme signaling
  - now CNC v1.0
  - CBC, CBCS
  - a) VideoCapabilities/AudioCapabilities object to include scheme types in video to play
  - b) Add scheme to mime type: canplay()
- EME on iOS "File a Radar"
- EME 2016 on Apple Platforms coming to a Safari Technology Preview near you
  - EME 2016 includes persistent key storage for offline
  - Why bother doing offline HLS vs MP4? HLS ServiceWorker would need to work vs single file mp4.
  - ServiceWorkers in Webkit ("Savior" of webkit): Make sure it works with video playback
- HLS: + WideVine, PlayReady
- HLS CMAF iosbmff
- Firefox Widevine
- For WebGL we need a write only mode for passing a decoded frame to a texture (is texture secure? css 3d?)
- Safari requires Hardware acceleration to do EME decryption
  - EME VideoCapabilities takes list and MediaCapabilities API does not and requires a lot of specificity

### QoS

https://public.etherpad-mozilla.org/p/FOMS-2017-quality

- Bots
- Browser App suspended?
  - Network
  - JS
- Use server and client time - have to use both to find "best" range

### Misc

Abortable Fetch
https://developers.google.com/web/updates/2017/09/abortable-fetch

## 10/04/2017

### Offline

https://public.etherpad-mozilla.org/p/FOMS-2017-offline

- Ship offline viewing (CBS, Bitmoving, Kaltura - mostly in SDKs, PWA coming or via Shaka)
  - QoS/QoE and analytics?
    - pings are queued up by ServiceWorker and sent when online
    - Types of data useful for offline
  - Android Chrome
  - Coming to Safari/iOS (need HLS/media player integration)
- Shaka supports offline
  - How do we adopt it?
  - What if the media uses DRM?
    - EME for offline is missing in browsers
      - Widevine/Clearkey encrypted coming in M62 of Android Chrome
      - Need to check with Mozilla and with Apple in Safari Tech Preview
- Which media source is best suited for offline (CBS)
  - Single bitrate to avoid ABR behavior and requiring all qualities to be delivered
  - A good case for HEVC/VP9 at 250kbps is watchable
- In PWA _only_ vs on page (Bitmovin)
  - https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API
  - https only
  - ServiceWorker registered for a single origin?
      - Will media and script CDN and origin need to match?
- Network offline
  - navigator.onLine: true (everybody - inconsistent across browsers - poll vs listen for event)
  - navigator.connection (Chrome desktop and Android only) https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API
    - downlink: 0.2
    - effectiveType: "4g"
    - onchange: null
    - rtt: 100
  - connection.metered (not well supported) https://developer.mozilla.org/en-US/docs/Web/API/Connection/metered
- Background fetch
  - In Chrome Canary (Kaltura w/ Shaka)
  - In Shaka PWA demo https://github.com/google/shaka-player/issues/879
- Prefetch for faster start (not offline) assuming there is a high probability user will watch it next
 
### MediaCapabilities

https://public.etherpad-mozilla.org/p/FOMS-2017-mediaCapabilites-QoE

- Allow improved selection of media sources by supplying information about whether configuration can be played, well and efficiently
- Will use hardware decoding? Not going to happen
  - Avoid renditions that will run on software because of size < 320p
    - These renditions will not leverage DRM properly
    - Video and player rendering problems switching from SW to HW
  - Avoid renditions that are too large (efficiency)
- VP9 new codec string is verbose enough to include color information
  - MIME type "media profile" (not exactly codec profile) attribute not allowed (mp4/video profile=high)
- MediaCapabilities takes JSON object which essentially is the contents of the MOV atom
Input `decodingInfo({media decoding configuration})`
```
dictionary VideoConfiguration {
  required DOMString contentType;
  required unsigned long width;
  required unsigned long height;
  required unsigned long bitrate;
  required double framerate;
};
dictionary AudioConfiguration {
  required DOMString contentType;
  DOMString channels;
  unsigned long bitrate;
  unsigned long samplerate;
};
```
Output
```
interface MediaCapabilitiesInfo {
  readonly attribute boolean supported;
  readonly attribute boolean smooth;
  readonly attribute boolean powerEfficient;
};
```
- Chrome will evaluate each playback to create a model of how well devices support media configurations
  - supported, smooth and powerEfficient will get better over time
- Ship behind a flag in 63 (hopefully in the next two weeks)
- Available with [runtime enabled feature](https://www.chromium.org/blink/runtime-enabled-features) something like `--enable-blink-features=media-capabilities`

### QoE

https://public.etherpad-mozilla.org/p/FOMS-2017-QoE-Implementation

- Standard Video and Player Performance Metrics
  - "Session" event graph (Rob could write a block post)
    - Page View / Player View / Video View
    - Show state machine -> Page, Embed, Play, Page Unload (Abandonment)
    - How do you explain when things are not 1-1 (request - response/error/abandonment)?
  - % of views that have rebuffering
    - average amount time rebuffering per view
- Break down startup time
  - page load, player setup, preload preload, play requested, media requested
- Race ad preroll vs video - use whichever is fastest
- tracking "canstart" vs "playing" vs "timeupdate" > 0
- Bitrate, Resolution - impacted by speed?
- Upscaling for quality
- Downscaling for cost
- Track user selecting quality from (and to) using ABR (pick 1080p, or back to auto, or low)
- "waiting" is a reliable way to determine ready state changing to a value lower than HAVE_FUTURE_DATA
  - The player is in a "playing" (not .paused) state but cannot continue
  - It can fire before playback indicating playback is not immediate
  - Firing after playback has started indicates a risk of underbuffering
- Performance Timing and Observer
  - Time to meaningful paint... time to play?
  - longtask tells you when JS ran for over 50ms... how about App suspended?
- Browser App suspended
  - No API
  - "Heartbeat" JS intervals or requestAnimationFrame is used to detect browser suspension
 
### Mobile Web

https://public.etherpad-mozilla.org/p/FOMS-2017-Mobile-Web

- Chrome is looking for feedback on going from Native Apps to Web or back
  - Vimeo supports both Native Apps and Mobile Web
    - Usage and development of the app is very different than the mobile site
     - User base vs. number of views per month can be very different between the two, but both use cases have value
     - Ex: Privacy may not be as much of an issue in a native app
- Cheaper user acquisition for web app
  - Youtube has both; mobile site can load and start video playback faster than the Youtube app
  - NYT has several native apps and PWA experiments
- Not having Fairplay available on iOS Safari requires a Native App for DRM content consumption
- Android 4.4+ has Chrome
  - older Android phones are still out in the wild and need to be addressed in some markets
__PWAs: Progressive Web Apps__
 - Done right, your mobile site becomes as good as a native app!
  - Add to homescreen
  - Offline mode
    - ServiceWorker Background Fetch
      - Can also be used to improve start time
      - Progress monitoring of fetch is coming soon
  - Push Notifications
- PWA Demos:
  - http://bit.ly/pwa-media
  - http://bit.ly/pwa-media-code
  - Dale Curtis has put a lot of work into these features
  - John Pallett happy to talk more about what PWAs can do
- Chrome dev summit later this month
- Features enabled by PWAs
  - Pages added to the homescreen are allowed to autoplay
- Muted autoplay is always allowed on Chrome Android and Safari iOS now

__AMP: Accelerated Mobile Pages Project is still around and being improved__

- GPU decoder to Canvas for 360 may not happen for DRM
  - might require a new feature on media elements or CSS transforms
  - secure textures not secure enough

- MSE on iOS: file a radar

- request device lock (There's an API for this)
- iOS screen could lock (sleep) if video is stalled for a certain amount of time
  - Should not happen with correct conditions (audio, looping...)

- Firefox on Android runs video decoder out of process to recover from failures

__Battery and Data consumption is a concern__
- data saving mode - "metered" connection
- Chrome data saving proxy compresses data (page, image, video)  sent to Chrome
  - How can the developers determine this is active?
    - Yes with "save-data" http header
  Detecting when device is data-sensitive:
    http://wicg.github.io/netinfo/
    https://developers.google.com/web/updates/2016/02/save-data
    https://developer.chrome.com/multidevice/data-compression
  - Can we show which sites use more data?
    - Think "battery settings" screen usage by app, but for data by origin

__Support__
- Can we bet on PWAs? 
  - Should there be an "PWApp Store" or is that search? Bing adds PWAs it finds into the Windows App Store
  - They are not meant to replace a native app (at least not in all cases, e.g. app requires APIs that aren't avaliable on web)
- ServiceWorkers are in development in WebKit https://webkit.org/status/#specification-service-workers

- Bug: oncue event is not fired in some versions of Android with native HLS playback (A: don't use native player to play HLS in Chrome Android)
  - HLS with fragmented MP4 is the way to go on Android using MSE. Not native player.

- Can you add a PWA from a webview (Facebook webview)?
  - Not so much. You can from a Chrome Panel

