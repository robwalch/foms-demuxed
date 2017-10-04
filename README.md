# FOMS/Demuxed San Francisco 2017

## FOMS notes

https://public.etherpad-mozilla.org/p/FOMS-2017

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
