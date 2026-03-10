IUS Visualizer Preview v2

IUS Visualizer renders a real-time circular visualizer synchronized to
local audio using the Web Audio API's FFT analyser, the Canvas 2D API,
and MediaRecorder for export.
The following upgrades were applied:

1. TRUE FFT AUDIO REACTIVITY
   The waveform, ring, dots, bloom, dust particles, and color are now all
   driven directly by the Web Audio API AnalyserNode. Every visual element
   responds to the actual frequency content of the loaded audio in real time.
   Previously, all motion was procedural (sine-based) and independent of audio.

2. LOGARITHMIC FREQUENCY MAPPING
   Bar heights are now distributed along a logarithmic frequency scale
   rather than a linear one. This matches how human hearing perceives pitch,
   producing much more musical and accurate visual representation — bass,
   mid, and treble ranges each occupy proportional visual space.

3. BEAT DETECTION
   A rolling energy history is maintained to detect transients (beats).
   When a beat is detected, particle emission spikes, the ring pulses, and
   a subtle screen-wide flash fires. Beat sensitivity is user-controllable.

4. DELTA-TIME ANIMATION LOOP
   The animation loop now uses timestamp-based delta time instead of
   assuming 60 fps. Motion speed is consistent regardless of frame rate or
   display refresh rate (60Hz, 120Hz, etc.).

5. MULTI-PASS BLOOM GLOW
   A dedicated Bloom Intensity control adds a second blurred draw pass
   behind bars and the ring, simulating soft bloom without WebGL.
   The bloom strength scales with bass energy.

6. AUDIO-REACTIVE HUE SHIFT
   A Color Shift slider allows the wave color to rotate through the hue
   spectrum as bass energy increases, producing dynamic chromatic breathing.

7. BATCHED CANVAS DRAW CALLS
   All dot arcs for a given frame are now batched into a single beginPath()
   call before fill(), significantly reducing Canvas 2D state changes and
   improving performance at high bar counts.

8. THROTTLED SEEK UI
   The seek bar and time readout are throttled to update no more than once
   per 200ms, eliminating unnecessary DOM thrashing on every animation frame.

9. LIVE ENERGY METERS
   Three real-time horizontal meters (BASS / MID / HI) are displayed below
   the seek bar, giving visual feedback on the audio signal being analysed.

10. CONFIGURABLE FFT SIZE
    A new FFT Size slider lets you switch between 256, 512, 1024, and 2048
    bins. Higher values give finer frequency resolution; lower values are
    more responsive and lighter on CPU.

11. RING PULSE CONTROL
    A dedicated Ring Pulse slider independently scales how much the ring
    expands on bass hits, decoupled from general motion settings.

12. LIVE VALUE READOUTS
    Every control slider now shows its current value inline, removing the
    need to hover or guess.

13. SECTION HEADERS IN CONTROLS
    Controls are grouped into labeled sections: Wave, Beat & Bloom, Dust,
    and Layers — making the panel easier to navigate.

14. REFINED UI TYPOGRAPHY
    The interface uses Syne (display) and DM Mono (controls) for a more
    considered visual identity, loaded from Google Fonts with a graceful
    system font fallback.


INPUTS

Music       — local audio file (MP3, WAV, M4A, AAC, OGG, FLAC)
Media       — foreground artwork or video inside the circular mask
Background  — full-frame image or video behind the visualizer

Wave section:
  Bars            — number of frequency bars (20–64)
  Glow            — shadow glow intensity
  Motion          — amplitude multiplier for bar height
  Audio sync      — blend between FFT-driven and procedural motion (0–100)
  Smoothing       — inter-frame frequency smoothing (higher = more buttery)
  Wave width      — horizontal span of the waveform
  Wave height     — maximum bar amplitude
  Dot size        — radius of individual waveform dots
  Softness        — softness of the glow column behind each bar
  Fade edge       — width of the transparency fade at waveform edges
  Ring thickness  — stroke width of the circular ring
  Wave color      — base color (hue-shifted by Color Shift at runtime)

Beat & Bloom section:
  Beat sensitivity  — threshold multiplier for beat detection (higher = more beats)
  Bloom intensity   — strength of the multi-pass blur bloom effect
  Color shift       — amount of hue rotation driven by bass energy (0 = none)
  Ring pulse        — scale of ring expansion on bass/beat events
  FFT size          — analyser resolution (256 / 512 / 1024 / 2048 bins)

Dust section:
  Amount, Delay/trail, Speed, Size, Spread, Brightness
  — control the behaviour of floating wavelet dust particles

Layers section:
  Visualizer scale / X / Y   — position and size of the circular badge
  Media scale / X / Y / opacity — foreground artwork inside circle
  Background scale / X / Y / opacity — full-frame background layer
  Canvas size — output resolution preset (1400×800 through 3840×2160)


RECORDING
Click "Record" to begin capturing the canvas at 60 FPS via MediaRecorder.
Audio is included in the recording when available (Web Audio routing).
Click "Stop" to download the result as a WebM file.
Codec preference can be selected from the dropdown (VP9 recommended).
Video bitrate: 12 Mbps. Audio bitrate: 192 kbps.


RENDERING PIPELINE (each frame)
1. Clear canvas
2. Draw background asset (fitted, scaled, offset)
3. Compute visualizer centre point and radius
4. Read FFT data from AnalyserNode; apply log-scale bin mapping
5. Compute energy bands (bass, mid, treble, overall, pulse)
6. Run beat detection against rolling energy history
7. Draw foreground media clipped to inner circle
8. Draw bloom pass (blurred ring) if bloom > 0
9. Draw ring with glow, scaled by bass pulse
10. Batch-draw waveform dots (single fill call per bar)
11. Draw softness / bloom column pass (screen composite)
12. Draw dust particles (wavelet shapes)
13. Apply edge fade mask (destination-in)


BROWSER REQUIREMENTS
A modern Chromium-based browser is strongly recommended.
Required APIs:
  Canvas 2D API
  Web Audio API (AudioContext, AnalyserNode, MediaElementSourceNode)
  HTML audio/video playback
  URL.createObjectURL()
  requestAnimationFrame()
  MediaRecorder + canvas.captureStream()


INSTALLATION
1. Download iusv_upgraded.html
2. Open it directly in a modern browser (no server required)
3. Load a music file
4. Optionally load a foreground media or background asset
5. Adjust controls
6. Record output if needed


KNOWN LIMITATIONS
- Recording output is limited to browser-supported WebM codecs
- Browser autoplay policy may require a manual Play press after loading audio
- Performance scales with canvas size, bar count, particle count, and bloom blur
- The bloom filter uses CSS blur which can be expensive at large canvas sizes;
  reduce bloom or use a lower canvas preset if frame rate drops
- Audio from MediaElementSource requires crossOrigin = "anonymous" on the element;
  local file loading handles this correctly by construction

MIT — see LICENSE file
