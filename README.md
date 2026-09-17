# Reverie Film

### Make films, not content.

**Turn everyday moments into something cinematic.**

Reverie Film is a free, browser-based cinematic video editor developed by [QuadraJ](https://quadraj.com).

No installation. No account. No complicated setup.

Open your browser, import your footage, and start making a film.

**[Launch Reverie Film →](https://reverie.quadraj.com)**

**Status:** Public Beta · v0.1.x

---

## Overview

Sometimes, all it takes is a different frame to turn an ordinary moment into something that feels like a film.

Reverie Film brings cinematic aspect ratios, creative color grading, spatial audio, and essential editing tools into a single browser-based workspace.

Rather than recreating every feature of a professional editing suite, Reverie focuses on making cinematic expression accessible without sacrificing creative control.

Whether you're experimenting with your first short film or simply capturing everyday moments, Reverie lets you start with the footage you already have.

**No downloads. No sign-ups. No watermarks. Just filmmaking.**

---

## Features

### 01. Cinematic framing & picture versions

Change how your story is seen.

* Cinematic aspect ratios, including Flat, CinemaScope, Academy, and custom formats.
* Independent picture versions for different output formats.
* Per-shot reframing without changing the original edit.
* Composition guides and direct manipulation in the Viewer.
* Support for different project and export resolutions.

A familiar street, an ordinary afternoon, or a moment with friends can feel entirely different through a cinematic frame.

### 02. Color grading & creative looks

Build the atmosphere of your film.

* A collection of Basic, Cinema, and Reverie Original Looks.
* Adjustable preset intensity.
* Exposure, contrast, saturation, temperature, and other color controls.
* RGB curves and LUT support.
* Creative effects including grain, bloom, diffusion, halation, and vignette.
* Histogram, waveform, and RGB parade scopes.

Start with a preset, then refine the details as much as you like.

### 03. Interactive 3D spatial audio

Sound is part of the scene.

Reverie includes a Three.js-powered 3D interface for positioning sound around the listener.

* Drag and position sound sources in a visual 3D environment.
* Control position, gain, spread, and other spatial properties.
* Animate sound positions using keyframes.
* Monitor binaural spatial audio through headphones using HRTF.
* Work with stereo beds and spatial sound objects.
* Export a separate six-channel 5.1 PCM WAV mix.

The interface uses Three.js for visualization and Web Audio for audio processing. Actual monitoring and output capabilities depend on the device and browser.

**Note:** This is not a Dolby Atmos implementation or certification.

### 04. Essential editing tools

Everything starts with the footage.

* Import video, audio, and still images.
* Arrange clips on a multitrack timeline.
* Trim, split, duplicate, move, and delete clips.
* Use snapping, transitions, fades, and linked audio/video editing.
* Work with frame-accurate timeline controls.
* Preview and export through a shared rendering pipeline.
* Save projects locally in the browser.

The workspace prioritizes the tools that matter to filmmaking while keeping advanced controls available when needed.

### 05. Local AI masking & tracking — Beta

Select a subject and let AI follow it across a shot.

Reverie's AI Mask tool uses Meta's SAM 2.1 Tiny model through a local ONNX inference pipeline.

* Point-based subject and exclusion selection.
* Temporal subject tracking within a shot.
* Subject and background color adjustments.
* Tracking corrections and selected-range processing.
* Local model caching for reuse.

AI inference runs on your device. Model and runtime files are downloaded when required, but your source footage and frames are not uploaded for AI inference.

**Performance notice**

AI tracking is experimental and has not yet been fully optimized. It may be slow, especially on long clips, complex scenes, or devices without suitable GPU acceleration.

Processing time depends on the browser, operating system, GPU, and selected footage. Tracking accuracy may also vary.

We are actively working on performance and reliability.

---

## Browser compatibility & requirements

Reverie is designed for modern desktop browsers. Because video editing relies on browser-level media and graphics APIs, feature availability varies across devices.

| Environment              | Support / Recommendation                                                      |
| ------------------------ | ----------------------------------------------------------------------------- |
| Google Chrome / Chromium | Recommended for the most complete experience                                  |
| Mozilla Firefox          | Common editing workflows supported; some features use compatibility paths     |
| Safari                   | Full feature compatibility has not been verified                              |
| Mobile browsers          | Responsive interface; full editing and export compatibility is not guaranteed |
| WebGL2                   | Required for the main video compositor                                        |
| WebCodecs                | Used for supported decoding and encoding workflows                            |
| WebGPU                   | Preferred acceleration backend for local AI when available                    |
| WebAssembly              | AI inference fallback when WebGPU is unavailable or fails                     |
| IndexedDB / OPFS         | Used for project persistence and generated local data                         |

### Chrome & WebGPU

For AI tracking, we recommend a recent desktop version of Google Chrome with WebGPU support and a compatible GPU.

Reverie checks the available capabilities at runtime and prefers WebGPU for AI inference when the supported execution path is available.

However, **using Chrome does not guarantee WebGPU availability**. Support depends on your hardware, graphics drivers, operating system, browser configuration, and secure-context requirements.

If WebGPU initialization or a recognized execution failure occurs, Reverie can retry AI inference using the WebAssembly backend.

The fallback may be significantly slower.

### Firefox

Firefox supports the common editing workflow, including timeline editing, preview, and compatible export paths.

Some browser capabilities differ from Chromium. AI may use the WebAssembly backend, and media may need to be reconnected when reopening projects.

Where native AAC encoding is unavailable, Reverie attempts to use a locally loaded AAC encoder extension.

### General performance recommendations

* Use an up-to-date desktop browser.
* Enable hardware acceleration when available.
* Keep sufficient free storage for local projects, proxies, and AI models.
* For demanding footage, allow time for proxy generation.
* Use Chrome with a compatible GPU when experimenting with AI tracking.

4K footage, high-bitrate media, and complex effects may require substantial processing resources.

**Important:** Supported export formats and resolutions are determined through runtime capability checks. Not every browser or device supports every encoding combination.

---

## Technology stack

Reverie Film is built as a browser-native editing application, combining modern web technologies with GPU rendering and local media processing.

| Layer              | Technologies                           |
| ------------------ | -------------------------------------- |
| Frontend           | React, TypeScript, Vite                |
| UI & state         | Tailwind CSS, Zustand, Lucide          |
| Video rendering    | WebGL2, custom rendering pipeline      |
| Media processing   | WebCodecs, Mediabunny                  |
| Audio              | Web Audio API, HRTF spatial processing |
| 3D visualization   | Three.js                               |
| AI model           | SAM 2.1 Hiera Tiny                     |
| AI inference       | ONNX Runtime Web, WebGPU, WebAssembly  |
| Local storage      | IndexedDB, Dexie, OPFS                 |
| Testing            | Vitest, Playwright                     |
| Deployment         | Static application, Cloudflare Pages   |
| Model distribution | Cloudflare R2-backed asset delivery    |

### Rendering architecture

Reverie uses a shared media and rendering pipeline for preview and export.

Source media is decoded through WebCodecs and Mediabunny where supported, composited using WebGL2, and encoded into supported output containers.

The shared rendering path helps preserve consistency between what you see in the Viewer and the finished film.

For demanding source footage, Reverie can generate local editing proxies. Final export requires the original media rather than silently substituting a lower-quality proxy.

### Audio architecture

Spatial sound objects use a common 3D position model.

Three.js provides the interactive spatial editor, while the Web Audio API handles real-time sound processing and HRTF-based headphone monitoring.

A separate export mixer supports six-channel PCM WAV delivery.

### AI architecture

Reverie's local AI pipeline uses:

* SAM 2.1 Tiny for temporal video segmentation.
* ONNX Runtime Web for inference.
* WebGPU as the preferred acceleration backend.
* WebAssembly as a fallback backend.
* Web Workers for inference outside the main UI thread.
* OPFS for cached model files and tracking-mask data.

Model files are downloaded on demand from `models.quadraj.com`.

Inference does not require uploading your footage to an AI server.

---

## Privacy & local-first design

**Your film stays with you.**

Reverie does not require an account or upload source media to a remote editing server.

Project data is stored locally through IndexedDB, while generated data such as proxies and AI caches can be stored in OPFS.

Project files and source footage are handled locally.

**Please note:**

* Clearing browser site data may delete locally stored projects and caches.
* Some browsers require you to reconnect source files when reopening a project.
* Portable project manifests do not contain the original media.
* An internet connection is needed to access the website and download uncached AI model assets.

We recommend keeping your original media and independent backups of important projects.

---

## Export & interoperability

Reverie supports the following output formats when the required browser capabilities are available.

| Format | Purpose                                                    |
| ------ | ---------------------------------------------------------- |
| MP4    | Recommended general movie output; H.264/AAC                |
| MOV    | QuickTime-compatible H.264/AAC output                      |
| WebM   | Web delivery using supported VP9/AV1 and Opus combinations |
| WAV    | Separate audio delivery, including six-channel PCM         |

A slower real-time WebM compatibility path may be available when the required WebCodecs encoders are unsupported.

### DaVinci Resolve handoff

Reverie provides an FCPXML export path for continuing an edit in DaVinci Resolve.

A limited CMX 3600 EDL option is also available for picture-cut interchange.

Source media must be relinked. Reverie-specific looks, effects, crops, and spatial audio are not guaranteed to transfer faithfully.

This is an editing handoff, not a complete project conversion.

### Current format limitations

Reverie does not currently provide:

* ProRes encoding.
* MXF output.
* Digital Cinema Package (DCP) authoring.
* Dolby Atmos authoring or certification.

Cinema aspect ratios and DCI-related raster options should not be confused with DCP delivery.

---

## Free. No watermarks.

Reverie Film is free to use.

We do not insert watermarks, forced branded outros, or promotional overlays into your finished video.

An optional **Made with QuadraJ Reverie** message is available for sharing, and supported MP4/MOV files may contain descriptive application metadata.

Neither changes your picture or claims ownership of your work.

---

## Beta status & known limitations

Reverie Film is under active development.

Current areas for improvement include:

* AI tracking performance and reliability.
* Performance with long, high-resolution, or high-bitrate footage.
* Browser-dependent media encoding support.
* Local storage and file-reconnection differences between browsers.

The editor includes capability checks and compatibility paths, but some operations may be unavailable on certain devices.

**For important work, retain your original footage, keep backups, and verify the exported result before relying on it.**

---

## Project status & source code

This repository serves as the public information page for Reverie Film.

The application's source code is maintained privately and is not distributed through this repository.

This is **not an open-source distribution**. Public access to this README does not grant a license to the application's source code or proprietary assets.

Technical descriptions on this page are provided to explain the product and its architecture.

Feedback, bug reports, and suggestions are welcome.

---

## QuadraJ

Reverie Film is developed by QuadraJ as part of its creative software projects.

**Make films, not content.**

[Launch Reverie Film](https://reverie.quadraj.com) · [Visit QuadraJ](https://quadraj.com)
