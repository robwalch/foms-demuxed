# FOMS 2018

## FOMS Day 1 - 10/15/2018

** Rob Walch: Position Statement **
- Performance metrics, QoE
- Crossbrowser web player streaming solutions
- Less work to play video

**Interests and Topics**

- [CTA-2066] Streaming Quality of Experience Events, Properties and Metrics
	- playlists
	- session heirarchy
		- primary content (playlist)
		- primary content (show / episode / clip / video asset)
		- secondary content (ad break)
		- secondary content (ad pod)

- Performance
	- Player profiling with Matt 
		- ABR
			- Rebuffering
		- Start Time
		- Setup

- Cross-browser
	- streaming metadata support (ID3 in TS good, DATERANGE and custom not good)
		- DATERANGE SCTE-35 metadata in Safari browser, and vendor streams
			- Who is implementing support for X-CUE?
	- "crossorigin" withCredentials requests : Talk to Youenn Fablet
	- autoplay: play():Promise errors 
	- WebVTT rendering

- Metadata
	- Something better than SCTE-35

- MSE v-next
	- Worker context
	- latency
	- past buffer eviction
	- deprecations (multitrack buffer, create object url)
	- iOS Safari!

- HTML5MediaElement
	- "crossorigin" attribute for all HLS requests in Safari (or something else for file requests other than the parent manifest)
	- autoplay play():Promise errors

- Media Capabiities (Chrome, Firefox, Safari)

- Accessibility
	- WebVTT
		- Why JW Player uses the polyfill renderer...
			- Line sorting
			- Differences with Safari... ask Evol

- Safari
	- fullscreen
	- DATERANGE TextTracks, and decode errors

**Ideas**
- Fake no support for MSE/m3u8 to get an MP4
- Defer loading of provider (load while idle of during ad break... Asset loading flow chart (similar to setup flow))
- Extend Promises for various tasks and implement debug profiling and charting of async logic flow
- Profile performance by \<script\>

### Breakouts
- Infrastructure
	- RTMP as a standard / in the browser for injection
	    - getUserMedia -> WebRTC
	    - Just send CMAF GoP on whatever transport
	    - Could SRT replace RTMP on the inject side? point-to-point
	- Fallback rendition switching
	    - Use channels with ITF to make changes to the draft
	    - Rerequest master manifest
	        - Copy minimum update period from DASH
	- Working with CDN logs
	    - Comparing to Player analytics
        - Standard for media streaming logs
        
- Player Capabilities
    - What is the current frame? Same as last time polled?
    - Fullscreen issues
        - Changing context of video tag while staying in fullscreen
        - Captions list when textTracks change
        - When is the animation ended going to fullscreen
        - Use VideoQuality API to report on framerate etc...
        - PIP API coming to Chrome
    - Service Workers
        - Apple needs to figure out range-requests made to ServiceWorkers for usage with HLS
	    - "crossorigin" withCredentials requests : Talk to Youenn Fablet, See https://github.com/jwplayer/jwplayer/issues/3056
    - Video Tag Events
        - MediaError.message concat high level message (HTML5MediaElement WY spec)
        
- Media Capabilities
    - In 3 browsers (C, F, S) https://developer.mozilla.org/en-US/docs/Web/API/Media_Capabilities_API
        - No Edge
    - Limit adaptation switching based on decoder capabilities API
        - Lower rebuffer rate an start time
        - Only small decrease in quality
        - Less bytes over the network
        - Longer battery life
    - VP9 uses a less performant decoder
        - Impacts battery life on mobile devices, and CPU on desktop (fans spinning)
    - video source elements can include a mimetype which is good because the browser doesn't have to sniff the mimetype

## FOMS Day 2 - 10/16/2018

- Player Profiling
    Examples:
        - MUX bot-watcher: Spin-up a player and run tests
            - https://github.com/muxinc/bot-watcher
            - hls.js vs video.js hls (vhs)
            - Results not posted (yet?)
            - Video of Matt @ MUX https://www.youtube.com/watch?v=PWHvmvEgI4U&feature=youtu.be
            - Uses puppeteer
    Collaborating on Player Comparison
    - What do we want to benchmark?
        - mux at performance scale
        - mux single view event timeline (EXAMPLE?)
        - performance on specific clients and network conditions
        - Map realworld data (internet throughput riding the train) to network throttling
    - Each player will setup with the same video, same network conditions
    - Define these scenarios (ACTION)
    - Output these metrics...(ACTION)
        - Viewer experience
            - Rebuffering
            - Uninterrupted playback
            - Slow start up time
            - Errors
            - visual quality: start, average, max
            - Quality switching: auto (ABR) vs manual (menu)
            - Is the buffer evicted in either?
    - How fast can you switch up?
    - How slow can you switch down (close to the end of the buffer)?
    - What do users want?
    - What (AB) tests should players run - and what results would be good to share?
            - Ignore events a user would not notice < 50ms (Performance API long task)
        - Performance API metrics
        - Page load time (up to player setup)
    - Long task events
    - Resource timings (number of resources, latency)
        - Script loading size/time
        - Setup time
        - Upscale percentage: average, max
            - videoWidthOrHeight / playerWidthOrHeight
        - UI Resonsiveness
            - Time from click to play / seeked
        - Engagement ?
        - Isn't that more about content and QoE?
        - Features
        - Offline mode (pick a quality to watch later)
    - What are the common player features we should include in benchmarking?
        - media types: mp4, hls, dash
    - What players can we benchmark? Sample/trial libraries
        - video.js / Brightcove?
        - JW Player
            - Talk to Eric Boyd about developer edition
            - RW could share licensed library player 
    Considerations
    - Performance timing page load time (exclude or up to player setup/init)
    - Video delivery and player delivery throttling. Tests should host all assets)
    
    Real-world network traces
    - allows us to throttle with realistic conditions
    - paper: https://heim.ifi.uio.no/paalh/publications/files/mmsys2013-dataset.pdf
    - dataset http://skuld.cs.umass.edu/traces/mmsys/2013/pathbandwidth/
    - more data http://traces.cs.umass.edu/index.php/Mmsys/Mmsys
    
    Other notes
        - Does quality switching cause drop-off?
        - Quality menu usage is a good engagement signal
        - Is it used because they don't like the current experience, or they simply want a specific quality
    - API method chaining can impact performance (player.load().play() vs player.load().then(() => player.play())
    - Chatted about code coverage tools and usage
    
    SUMMARY
    - Define player benchmark scenarios (ACTION)
    - Define metrics ouput by running the test (ACTION)
    - Make it easy to integrate a player with bot-watcher (ACTION)
    - Throttle CPu, limit memory available, throttle or replay network conditions (ACTION)
    - Speak with folks who are interested Flavio and Matt MMCC... Who would like to drive this as an open-source project?

- Frame accuracy
    - Firefox can provide frame timestamp (behind a flag)
    - WebGL Extension to provide information about which frame is being sent to the renderer
    - Presumed framerate? VideoQuality API?
    - Two proposals for frame tagging - which one can we standardize on?
    - Use cases
        - What frame is rendered to a canvas for webGL rendering
        - current frame timecode (PTS or "number") timeOffset (per track) what time scale? Second doubles not good enough...
        - seek to next frame / paused frame by frame
        - frame accurate cues
        - versions of currentTime, fastSeek(), duration, and the TimeRanges accessors, in frames
    - W3C issue https://github.com/w3c/media-and-entertainment/issues/4
    - Browser test https://daiz.github.io/frame-accurate-ish/

- Autoplay
    - breaking features or websites
        - Can a dialog be opened before putting policies like this into effect
            - Chrome WCG Intervention
    - canAutoplay() API
        - Window level vs video element
        - What constraints will be added besides audio, gesture, etc?
        - Safari can use it as a signal to not play/pause
        - Maybe not for WebAudio and Text-to-Speech 

Notes:
    - play() Promise support in Edge?
    - Safari calls play() pause() (They would use any canAutoplay test to prevent this behavior)
    - Firefox will show a notification for sites that want to autoplay
        - This may be triggered by autoplay test
    - UserActivation API - expose a boolean for inUserActivation
    - Need a clock/timer for active application time
        - exclude timing when device/browser is locked - something better than heartbeat
    - calling load() on userGesture by checking global.event in player logic sucks
    - When you use the autoplay attribute:
        - Safari and Chrome will autopause when the video is scrolled offscreen (not removed from the DOM!)
    - Confirmation of getUserMedia() enables autoplay on Safari (will in Chrome 71)
    
    
**Tooling**
    - Devtools link from video element to media-internals
        - File an issue with Chrome (CR bug): internal-media
        - Chrome trace - filter "media" label

## demuxed

### 10/17-18/2018

- tensorflow can be configuration language, more than a tool used for ML

	- specify
	- unify
	- tensorfy

- bandwidth estimation is hosed with segment requests
	(see Will Law's talk)

- User's may not want to pay for quality at every second of playback
	(don't try so hard to upswitch with users who don't make manual selections)

- Did the user make a manual selection? They either care about quality, or their data plan.
