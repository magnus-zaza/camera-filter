# Hat Filter Studio

A real-time webcam filter application that overlays various animated hats on your face using advanced face tracking technology. Built with vanilla JavaScript and MediaPipe Face Mesh for accurate face landmark detection.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Language](https://img.shields.io/badge/language-JavaScript-yellow.svg)

## 🎩 Features

### Core Functionality
- **Real-time Face Tracking** - Uses MediaPipe Face Mesh for accurate 468-point facial landmark detection
- **Multiple Hat Types** - Choose from 5 different hat designs:
  - 👮 Police Hat - Classic police cap with customizable badge
  - 👨‍🍳 Chef Hat - Traditional white toque
  - 🧙 Wizard Hat - Purple magical hat with stars
  - 🤠 Cowboy Hat - Brown western-style hat
  - 🎉 Party Hat - Colorful celebration cone
- **Customizable Text** - Add personalized names or text to your hats
- **Adjustable Sizing** - Real-time hat size control (0.6x - 2.0x)
- **Fullscreen Mode** - Immersive fullscreen camera view

### User Interface
- **Bilingual Support** - Full English and Arabic (العربية) translations with RTL support
- **Responsive Design** - Works seamlessly on desktop, tablet, and mobile devices
- **Modern UI/UX** - Glass-morphism effects, smooth animations, and intuitive controls
- **Dark Mode Ready** - Adaptive color scheme support

### Technical Features
- **No Installation Required** - Runs entirely in the browser
- **Offline Capable** - Can use local library files when available
- **Cross-Browser Compatible** - Works on Chrome, Firefox, Safari, and Edge
- **Performance Optimized** - Smooth tracking with motion smoothing

## 📋 Table of Contents

- [Quick Start](#quick-start)
- [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [Browser Compatibility](#browser-compatibility)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)
- [Complete Code Documentation](#complete-code-documentation)

## 🚀 Quick Start

### Basic Usage

1. **Clone or download** this repository
2. **Open `index.html`** in a modern web browser
3. **Grant camera permissions** when prompted
4. **Click "Start camera"** to begin
5. **Select a hat type** from the dropdown
6. **Customize** with your name and adjust the size
7. **Enjoy!** The hat will automatically track your face

### Using Local Libraries (Offline Mode)

For offline use or better performance, download the required libraries into the `libs/` folder:
- `camera_utils.js`
- `face_mesh.js`
- `face_mesh_solution_packed_assets_loader.js`
- `face_mesh_solution_simd_wasm_bin.js`
- `face_mesh_solution_simd_wasm_bin.wasm`
- `face_mesh.binarypb`

The application will automatically detect and use local files if available.

## 🔍 How It Works

### Architecture Overview

```
User Interface → Camera Access → Face Detection → Landmark Extraction → Hat Positioning → Rendering
```

### Step-by-Step Process

1. **Camera Initialization**
   - Requests user permission for webcam access
   - Initializes video stream at 1280x720 resolution
   - Mirrors the video horizontally for natural user experience

2. **Face Mesh Setup**
   - Loads MediaPipe Face Mesh library (local or CDN)
   - Configures detection parameters:
     - `maxNumFaces: 1` - Tracks single face for performance
     - `refineLandmarks: true` - Enhanced accuracy
     - `minDetectionConfidence: 0.6` - Balance between accuracy and performance
     - `minTrackingConfidence: 0.6` - Smooth tracking

3. **Face Landmark Detection**
   - MediaPipe processes each video frame
   - Extracts 468 3D facial landmarks
   - Key landmarks used:
     - **Landmark 10** - Forehead top (hat vertical position)
     - **Landmark 234** - Left temple (hat width calculation)
     - **Landmark 454** - Right temple (hat width calculation)

4. **Hat Positioning Algorithm**
   ```javascript
   // Calculate head width
   headWidth = distance(landmark234, landmark454)
   
   // Calculate hat dimensions
   hatWidth = headWidth × 1.55 × sizeMultiplier
   hatHeight = hatWidth × aspectRatio
   
   // Position hat above head
   hatX = forehead.x (mirrored)
   hatY = forehead.y - (hatHeight × 0.55)
   ```

5. **Motion Smoothing**
   - Linear interpolation (lerp) smooths sudden movements
   - Smoothing factor: 0.25 (25% new position, 75% previous)
   - Reduces jitter while maintaining responsiveness

6. **Rendering**
   - SVG hat overlays positioned using absolute CSS transforms
   - Text dynamically scaled based on hat size
   - Opacity transitions for smooth appearance/disappearance

## 📁 Project Structure

```
camera-filter/
├── index.html              # Main application file (HTML + CSS + JS)
├── README.md              # This file
├── LICENSE                # MIT license
└── libs/                  # Optional: MediaPipe library files
    ├── camera_utils.js
    ├── face_mesh.js
    ├── face_mesh_solution_packed_assets_loader.js
    ├── face_mesh_solution_simd_wasm_bin.js
    ├── face_mesh_solution_simd_wasm_bin.wasm
    └── face_mesh.binarypb
```

## 📚 Code Documentation

Copyright (c) 2025 Magnus Zaza. All rights reserved.

Licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### Third-Party Libraries

This project uses the following libraries under the Apache License 2.0:
- MediaPipe Face Mesh
- MediaPipe Camera Utils

## Complete Code Documentation

For comprehensive documentation covering every aspect of the code, see **[CODE_DOCUMENTATION.md](CODE_DOCUMENTATION.md)**

This includes:
- **Detailed HTML Structure** - Every element explained
- **CSS Architecture** - All styles documented with examples
- **JavaScript Functions** - Full API documentation
- **Face Tracking Algorithm** - How landmark detection works
- **Translation System** - Adding new languages
- **Configuration Guide** - Customizing behavior
- **Performance Optimization** - Tips and tricks
- **Debugging Guide** - Troubleshooting help

## Author

**Magnus Zaza**

---

Built for fun video calls! 🎩🎉