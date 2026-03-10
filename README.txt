IUS Visualizer Preview 

Visualizer is a standalone browser based HTML application for loading local audio, image, and video assets into a real time canvas based music visualizer. It provides synchronized playback controls, configurable visual parameters, layered media composition, and client side recording output without requiring a build step, server process, or external framework.

Overview

The application is implemented as a single HTML file that combines structure, styling, and logic in one deployable asset. The user interface is designed for direct local use in a modern desktop browser and focuses on fast setup, live preview, and lightweight export.

The app supports three main input layers.

1. Music input for local audio playback.
2. Media input for foreground artwork or video displayed inside the circular visualizer.
3. Background input for full frame image or video composition.

Rendering is performed with the Canvas 2D API, while recording is handled through the MediaRecorder API using browser supported WebM codecs.

Features

1. Local audio file loading with playback support.
2. Seek control with current time and total duration display.
3. Real time preview rendered in the top section of the interface.
4. Adjustable waveform style visualizer controls including bar count, glow, motion, width, height, dot sizing, softness, and edge fade.
5. Foreground media and background media layer support for both images and videos.
6. Position, scale, and opacity controls for visualizer, media, and background layers.
7. Canvas preset switching for common output formats such as landscape, square, portrait, and 4K.
8. In browser recording of the rendered canvas to WebM.
9. Compact control layout for higher parameter visibility during editing.
10. Standalone deployment as a single HTML file.


HTML is used for layout and form controls.

CSS is used for the dark UI theme, compact panel layout, responsive behavior, and preview container sizing.

Vanilla JavaScript is used for application state, asset loading, playback control, animation, rendering, and recording.

The canvas is the primary render target. Each animation frame performs the following sequence.

1. Clear the current frame.
2. Draw the fitted background asset if present.
3. Compute the visualizer center point and radius from canvas size and user parameters.
4. Draw the circular ring.
5. Render the internal waveform bars and dots.
6. Render motion particles and glow effects.
7. Clip and draw foreground media within the circular mask.

The visual animation is procedural and time driven. It is not based on FFT audio analysis in the current implementation. Instead, the waveform style motion is generated from layered sine based motion functions. This makes the visual stable, lightweight, and independent of browser audio analysis APIs.


Audio is loaded from a local file through an invisible file input and attached to a dynamically created HTMLAudioElement. The audio element is used for playback, duration tracking, and seek updates.

The current implementation provides synchronized transport controls but does not currently drive the visualizer directly from frequency domain audio data.


Recording

Canvas recording is generated from canvas.captureStream(60), which produces a 60 FPS stream for MediaRecorder. The application selects a supported MIME type from a small WebM codec list and downloads the resulting recording automatically when capture stops.


Supported File Types

Audio input accepts common browser supported local formats including MP3, WAV, M4A, AAC, OGG, and FLAC.

Media and background inputs accept common image and video formats including PNG, JPG, JPEG, MP4, MOV, and WebM, subject to browser codec support.



IMPORTANT - Browser Requirements

A modern Chromium based browser is recommended. The following browser capabilities are required.

1. Canvas 2D API
2. HTML audio and video playback
3. URL.createObjectURL()
4. requestAnimationFrame()
5. MediaRecorder
6. canvas.captureStream()


No installation is required.

1. Download the HTML file.
2. Open it directly in a modern browser.
3. Load a music file.
4. Optionally load a foreground image or video.
5. Optionally load a background image or video.
6. Adjust the visual parameters.
7. Use the preview panel to inspect the composition.
8. Record the output if needed.


Limitations

1. The waveform animation is procedural and not true spectrum or waveform analysis from the audio signal.
2. Recording output is limited to browser supported WebM codecs.
3. Browser autoplay restrictions may require the user to manually press Play after loading audio.
4. Performance depends on canvas size, particle count, glow intensity, and the size of loaded media assets.
5. Audio is used for playback and transport control, but the current render logic does not yet map live audio frequency data to visual motion.
