
<a href="LICENSE">
  <img src="images/license-badge.png" alt="License badge" width="70">
</a>



## IUS Visualizer

https://iusmusic.github.io/IUSV/

## Standalone browser-based audio visualizer. 
Renders a real-time circular visualizer driven by local audio. Frequency
analysis is performed via the Web Audio API AnalyserNode using FFT. All
visual output is drawn to an HTML Canvas element using the Canvas 2D API.
Recording is handled by MediaRecorder. All processing is client-side.


INPUTS

Music       Local audio file. Accepted formats: MP3, WAV, M4A, AAC, OGG, FLAC.
Media       Foreground image or video rendered inside the circular mask.
Background  Full-frame image or video rendered behind the visualizer.

Video inputs loop automatically and play muted. Audio plays through the
Web Audio graph for analysis and is included in recordings.


CONTROLS

Bar style
  Selects the visual form used to render frequency data. Eight options:

  Dots          Stacked dot columns extending symmetrically above and
                below the wave centre. Dot count, gap, and radius scale
                with amplitude.

  Classic bars  Solid filled rectangles, symmetric up and down. Bar width
                is proportional to spacing.

  Mirror bars   Gradient-filled bars. Fully opaque at centre, transparent
                at tips. Amplitude controls gradient extent.

  Neon tubes    Two-pass stroke render. A narrow fully opaque inner line
                sits inside a wide soft halo. Glow and Bloom amplify the
                halo. Responds strongly to Softness.

  Radial circle Bars radiate outward from the centre of the circle. Each
                bar has an individual gradient from transparent at root to
                opaque at tip. Does not use the inner clip or edge fade.

  Arc burst     Arcs arranged around the ring circumference. Each arc
                spans and thickens proportionally to its frequency bin
                amplitude. Does not use the inner clip or edge fade.

  Ribbon wave   Bezier-smoothed filled shape. Upper and lower curves are
                drawn as a single closed path. Produces a fluid, organic
                waveform. Edge fade applies.

  Stacked blocks  Square tiles stacked above and below centre. Stack
                height scales with amplitude. Top block receives strongest
                glow.

Wave — Bars
  Number of frequency bins mapped to visual bars. Range: 20–64.

Wave — Glow
  Shadow blur radius applied to bars and ring. Higher values produce
  stronger ambient glow.

Wave — Motion
  Amplitude multiplier for bar height. Higher values produce taller bars.

Wave — Audio sync
  Blend between FFT-driven amplitude and procedural sine motion.
  100 = fully audio-driven. 0 = fully procedural.

Wave — Smoothing
  Inter-frame lerp applied to bar memory. Higher values produce slower,
  smoother transitions between frames.

Wave — Wave width
  Horizontal span of the waveform relative to the ring radius.

Wave — Wave height
  Maximum bar amplitude relative to the ring radius. Scales with bass
  energy at runtime.

Wave — Wave Y offset
  Vertical offset of the wave centre within the circle. 0 = geometrically
  centred. Positive values move the wave down.

Wave — Dot size
  Radius of individual dots in the Dots style. Also affects treble
  responsiveness of dot scaling.

Wave — Softness
  Applies a blurred screen-composite pass behind each bar. Increases
  perceived softness and ambient light.

Wave — Fade edge
  Width of the transparency fade at the left and right edges of the
  waveform. Does not apply to radial styles.

Wave — Ring thickness
  Stroke width of the outer circular ring. Scales with bass pulse.

Wave — Wave color
  Base color for all visual elements. Hue is shifted at runtime if
  Color shift is greater than zero.

Beat & Bloom — Beat sensitivity
  Detection threshold multiplier. Higher values register more beats.
  Detection uses a rolling 43-frame energy history against a dynamic
  average.

Beat & Bloom — Bloom intensity
  Strength of the secondary blurred draw pass applied to the ring and
  bars. Simulates optical bloom without WebGL. Scales with bass energy.

Beat & Bloom — Color shift
  Maximum hue rotation applied to the wave color, driven by bass energy.
  0 = no shift. 100 = full hue cycle at peak bass.

Beat & Bloom — Ring pulse
  Scale of ring radius expansion on bass and beat events. 0 = no pulse.

Beat & Bloom — FFT size
  AnalyserNode FFT resolution. Options: 256, 512, 1024, 2048 bins.
  Higher values improve frequency resolution. Lower values improve
  temporal responsiveness and reduce CPU cost.

Dust — Amount
  Number of floating wavelet particles rendered per frame.

Dust — Delay / trail
  Phase offset between particles. Higher values spread particle motion
  across time, producing a trailing effect.

Dust — Speed
  Rate at which particle position advances along its motion path.

Dust — Size
  Base size of each wavelet particle shape.

Dust — Spread
  Horizontal and vertical drift range of particles from their anchor bar.

Dust — Brightness
  Alpha multiplier for particle opacity.

Layers — Visualizer scale / X / Y
  Scale and canvas position of the entire circular badge.

Layers — Media scale / X / Y / Opacity
  Scale, position, and opacity of the foreground media layer inside the
  circle.

Layers — Background scale / X / Y / Opacity
  Scale, position, and opacity of the background layer.

Layers — Canvas size
  Output resolution. Presets: 700x400, 1400x800, 1920x1080, 1080x1080,
  1080x1920, 3840x2160. Changing this resets the canvas immediately.


RECORDING
Click Record to begin canvas capture at 60 FPS via MediaRecorder.
Click Stop to end capture and download the result as a WebM file.
Audio captured through the Web Audio graph is included in the recording
when available. Video bitrate: 12 Mbps. Audio bitrate: 192 kbps.
Codec can be selected from the dropdown. VP9 is recommended.


RENDERING PIPELINE
Each animation frame executes the following sequence:

1.  Clear canvas.
2.  Draw background asset, fitted and scaled.
3.  Compute visualizer centre point and radius.
4.  Read FFT byte frequency data from AnalyserNode.
5.  Apply logarithmic bin mapping across bars.
6.  Compute smoothed energy bands: bass, mid, treble, overall, pulse.
7.  Run beat detection against rolling energy history.
8.  Update live energy meters in the UI (throttled).
9.  Draw foreground media clipped to inner circle.
10. Draw bloom pass (blurred ring stroke) if bloom > 0.
11. Draw ring with glow shadow, scaled by bass pulse.
12. Compute per-bar heights from frequency data.
13. Dispatch to selected bar style renderer.
14. Draw softness / bloom column pass using screen composite.
15. Draw dust wavelet particles.
16. Apply edge fade mask using destination-in composite.
17. Restore canvas transform state.


AUDIO GRAPH
MediaElementSource -> AnalyserNode -> GainNode -> AudioContext.destination
                                               -> MediaStreamDestination

The MediaStreamDestination output is used to capture audio into the
recording stream. The GainNode is present for gain staging. The
AnalyserNode reads frequency and time-domain data each frame.


PERFORMANCE NOTES
- Bar count, particle count, bloom blur, and canvas resolution are the
  primary performance variables. Reduce these if frame rate drops.
- Bloom uses CSS filter blur on the Canvas 2D context. Cost scales with
  canvas resolution. Disable bloom at 3840x2160 unless hardware allows.
- Draw calls are batched where possible. Dot arcs per frame share a
  single beginPath / fill pair.
- Seek UI DOM updates are throttled to one write per 200ms.
- Animation uses delta-time scaling so motion is frame-rate independent.


BROWSER REQUIREMENTS
Chromium-based browser strongly recommended.

Required APIs:
  Canvas 2D API
  Web Audio API (AudioContext, AnalyserNode, MediaElementSourceNode,
                 GainNode, MediaStreamDestination)
  HTMLAudioElement / HTMLVideoElement
  URL.createObjectURL()
  requestAnimationFrame()
  MediaRecorder
  canvas.captureStream()


INSTALLATION
1. Download iusv.html.
2. Open directly in a modern browser. No server required.
3. Load a music file.
4. Optionally load a media file and a background file.
5. Select a bar style and adjust controls.
6. Click Record to capture output.


KNOWN LIMITATIONS
- Recording is limited to WebM container with VP8 or VP9 video codec.
- Browser autoplay policy may block audio on first load. Press Play.
- Bloom blur cost increases with canvas resolution.
- Audio source connection via createMediaElementSource is one-time per
  element. Clearing and reloading music creates a new source node.
- Local file loading sets crossOrigin to anonymous by construction.
  Remote URLs may require appropriate CORS headers.


## License

I/US Source-Available License 1.0

Copyright (c) 2026 Pezhman Farhangi  
I/US Music

Summary

This repository and its contents are made available for viewing, reference, study, and limited private internal evaluation only.

Permitted Use

You may:

- view the source code and documentation
- download the repository for personal reference, study, and internal evaluation
- make private modifications for personal, non-commercial evaluation only

Restrictions

You may not, without prior written permission from I/US Music:

- sell this software or any modified version of it
- distribute, sublicense, publish, or share this software or any modified version of it outside the limited permissions required for access through GitHub
- use this software or any modified version of it for commercial purposes
- create or distribute derivative works for public or commercial release
- offer this software as part of a product, service, platform, device, or commercial package
- redistribute source code, compiled builds, packaged versions, or modified copies
- remove or alter copyright, ownership, or license notices

GitHub Platform Notice

If this repository is hosted publicly on GitHub, GitHub users may have certain limited rights to view and fork the repository through GitHub’s own platform functionality, as required by GitHub’s Terms of Service. No permission is granted beyond those minimum platform rights unless explicit written permission is given by I/US Music.

## Ownership

All rights not expressly granted under this license are reserved.

This license does not transfer ownership of the software, documentation, designs, concepts, hardware direction, brand identity, or any related intellectual property.

No Trademark Rights

This license does not grant any right to use the names I/US, IUS, I/US Music, the official logo, the visual identity, artwork, images, audio branding, or any other protected brand assets.

Trademark and brand use are governed separately.

No Warranty

This software, hardware prototype, and all associated materials are provided "as is", without warranty of any kind, express or implied, including but not limited to merchantability, fitness for a particular purpose, and noninfringement.

In no event shall the author or copyright holder be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software.

Contact

For licensing requests, commercial rights, redistribution requests, or permission to use protected brand assets, prior written permission must be obtained from I/US Music.



<a href="LICENSE">
  <img src="images/license-badge.png" alt="License badge" width="70">
</a>
