# Hat Filter Studio - Complete Code Documentation

This document provides comprehensive documentation for every component of the Hat Filter Studio application.

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [HTML Structure](#html-structure)
3. [CSS Documentation](#css-documentation)
4. [JavaScript Documentation](#javascript-documentation)
5. [Face Tracking Algorithm](#face-tracking-algorithm)
6. [Translation System](#translation-system)
7. [Configuration Guide](#configuration-guide)

---

## Architecture Overview

### Application Flow

```
┌─────────────────┐
│  User Opens App │
└────────┬────────┘
         │
         v
┌─────────────────────┐
│ Initialize UI       │
│ - Set Arabic lang   │
│ - Load translations │
│ - Setup event       │
│   listeners         │
└────────┬────────────┘
         │
         v
┌─────────────────────┐
│ User clicks         │
│ "Start Camera"      │
└────────┬────────────┘
         │
         v
┌─────────────────────┐
│ Load MediaPipe      │
│ Libraries           │
│ - camera_utils.js   │
│ - face_mesh.js      │
└────────┬────────────┘
         │
         v
┌─────────────────────┐
│ Request Camera      │
│ Permission          │
└────────┬────────────┘
         │
         v
┌─────────────────────┐
│ Start Video Stream  │
│ 1280x720 @ 30fps    │
└────────┬────────────┘
         │
         v
┌─────────────────────┐
│ For Each Frame:     │
│ 1. Capture image    │
│ 2. Detect face      │
│ 3. Extract landmarks│
│ 4. Calculate pose   │
│ 5. Smooth motion    │
│ 6. Render hat       │
└────────┬────────────┘
         │
         v
    (Loop until stopped)
```

---

## HTML Structure

### Document Head

```html
<!doctype html>
<html lang="ar" dir="rtl">
```
- **lang="ar"**: Sets Arabic as default language
- **dir="rtl"**: Enables Right-to-Left text direction for Arabic

### Meta Tags

```html
<meta charset="utf-8" />
```
- Specifies UTF-8 encoding for international characters (Arabic support)

```html
<meta name="viewport" content="width=device-width, initial-scale=1" />
```
- Makes the page responsive on mobile devices
- `width=device-width`: Match screen width
- `initial-scale=1`: No zoom on load

### External Resources

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
```
- **preconnect**: Establishes early connection to Google Fonts servers
- Improves font loading performance

```html
<link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
```
- Loads Cairo font family (optimized for Arabic)
- Weights: 400 (regular), 600 (semi-bold), 700 (bold)
- `display=swap`: Shows fallback font immediately, swaps when custom font loads

### Main HTML Structure

```html
<div class="language-selector">
  <select id="languageSelect">
    <option value="en">English</option>
    <option value="ar" selected>العربية</option>
  </select>
</div>
```
**Purpose**: Language switcher
- `selected` on Arabic option makes it the default
- Positioned absolutely in top corner (RTL aware)

```html
<div class="app-shell">
```
**Purpose**: Main container with max-width constraint
- Responsive: `width: min(960px, 100%)`
- Centers content on large screens

```html
<header>
  <h1 class="hero-title" data-i18n="title">...</h1>
  <p class="hero-subtitle" data-i18n="subtitle">...</p>
</header>
```
**Purpose**: Page title and description
- `data-i18n`: Marks element for translation
- Text content is replaced when language changes

```html
<div class="control-bar">
```
**Purpose**: Main control panel
- Grid layout: `grid-template-columns: repeat(auto-fit, minmax(200px, 1fr))`
- Automatically wraps on small screens

```html
<select id="hatType">
  <option value="police">Police</option>
  <option value="chef">Chef</option>
  ...
</select>
```
**Purpose**: Hat type selector
- Each value corresponds to a hat SVG ID

```html
<input id="name" type="text" data-i18n-placeholder="namePlaceholder" />
```
**Purpose**: Custom text input for hat overlay
- `data-i18n-placeholder`: Translates placeholder text
- `autocomplete="off"`: Prevents browser suggestions

```html
<div id="stage" class="stage">
```
**Purpose**: Video preview container
- `aspect-ratio: 16/9`: Maintains video proportions
- Position: relative (for absolute children)

```html
<video id="video" playsinline autoplay muted></video>
```
**Purpose**: Webcam video element
- `playsinline`: Prevents fullscreen on iOS
- `autoplay`: Starts playing immediately
- `muted`: Required for autoplay in most browsers
- CSS: `transform: scaleX(-1)` mirrors video horizontally

```html
<div id="hat" class="hat">
  <svg id="hat-police" class="hat-svg">...</svg>
  <svg id="hat-chef" class="hat-svg" style="display: none;">...</svg>
  ...
  <div id="hatName" class="hat-name"></div>
</div>
```
**Purpose**: Hat overlay container
- Multiple SVG children (one per hat type)
- Only one visible at a time
- `hat-name` div for custom text overlay

---

## CSS Documentation

### CSS Custom Properties (Variables)

```css
:root {
  color-scheme: light dark;
}
```
- Enables browser's native dark mode support
- Affects form controls and scrollbars

### Font Stacks

```css
font-family: "Inter", system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
```
**Fallback chain**:
1. Inter (custom web font)
2. system-ui (OS default)
3. -apple-system (macOS/iOS)
4. BlinkMacSystemFont (Chrome on macOS)
5. Segoe UI (Windows)
6. sans-serif (generic fallback)

```css
[dir="rtl"] {
  font-family: "Cairo", "Inter", ...;
}
```
- Cairo font prioritized for RTL (Arabic)
- Better glyph support for Arabic characters

### Layout Systems

#### Flexbox (Main Layout)

```css
body {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
- Centers content both horizontally and vertically
- Full viewport height: `min-height: 100vh`

#### Grid (Control Bar)

```css
.control-bar {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}
```
- `auto-fit`: Creates as many columns as fit
- `minmax(200px, 1fr)`: Each column minimum 200px, shares remaining space
- `gap`: Space between grid items

### Responsive Typography

```css
font-size: clamp(32px, 4vw, 44px);
```
**clamp() function**:
- Minimum: 32px (small screens)
- Preferred: 4vw (4% of viewport width)
- Maximum: 44px (large screens)
- Automatically scales between min and max

### Gradient Backgrounds

```css
background: linear-gradient(135deg, #0f172a 0%, #1e293b 35%, #0b1120 100%);
```
- `135deg`: Diagonal angle (top-left to bottom-right)
- Three color stops for smooth transition
- Dark blue theme

### Glassmorphism Effect

```css
backdrop-filter: blur(10px);
background: rgba(15, 23, 42, 0.8);
```
- `backdrop-filter`: Blurs content behind element
- Semi-transparent background for "frosted glass" effect

### CSS Transforms

```css
transform: translate(-50%, -50%);
```
- Centers absolutely positioned element
- Used with `left: 50%; top: 50%;`

```css
transform: scaleX(-1);
```
- Flips video horizontally (mirror effect)
- Negative scale inverts on X-axis

### Transitions

```css
transition: all 0.2s ease;
```
- `all`: Animate all properties
- `0.2s`: 200 milliseconds duration
- `ease`: Slow start and end, faster in middle

### Animations

```css
@keyframes button-spin {
  to { transform: rotate(360deg); }
}
```
- Rotates element 360 degrees
- Used for loading spinner
- Applied with: `animation: button-spin 0.8s linear infinite;`

### Positioning

```css
.hat {
  position: absolute;
  top: 32%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```
- `absolute`: Position relative to nearest positioned ancestor (stage)
- JavaScript dynamically updates `top` and `left`
- `transform` keeps hat centered on calculated point

### Media Queries

```css
@media (max-width: 720px) {
  .secondary-controls {
    flex-direction: column;
  }
}
```
- Applies styles when screen width ≤ 720px
- Stacks controls vertically on mobile

```css
@media (prefers-color-scheme: dark) {
  .card {
    background: rgba(15, 23, 42, 0.72);
  }
}
```
- Detects OS dark mode preference
- Adjusts colors automatically

---

## JavaScript Documentation

### Global Constants

```javascript
const CAMERA_UTIL_URLS = [
  'libs/camera_utils.js',  // Local file (highest priority)
  'https://cdn.jsdelivr.net/...',  // CDN fallback
  'https://unpkg.com/...',         // Alternative CDN
  'https://fastly.jsdelivr.net/...' // Third option
];
```
**Purpose**: Fallback chain for loading camera utilities
- Tries local file first (offline/faster)
- Falls back to CDN if local not available
- Multiple CDN sources for reliability

```javascript
const HAT_ASPECT = 380 / 600;
```
**Purpose**: Maintains hat proportions
- All hat SVGs use viewBox="0 0 600 380"
- Height = Width × 0.633

```javascript
const SMOOTHING = 0.25;
```
**Purpose**: Motion smoothing factor
- 0.25 = 25% new position, 75% previous
- Lower value = smoother (but more lag)
- Higher value = more responsive (but jittery)

### State Variables

```javascript
let faceMesh = null;
```
- Stores MediaPipe FaceMesh instance
- `null` until libraries loaded and initialized

```javascript
let camera = null;
```
- Stores Camera utility instance
- Manages video stream and frame processing

```javascript
let smoothedPose = null;
```
- Stores interpolated hat position {x, y, width}
- `null` triggers reset (immediate snap to new position)

```javascript
let trackingActive = false;
```
- Boolean flag for tracking state
- Prevents processing when camera stopped

```javascript
let sizeMultiplier = parseFloat(sizeRange.value);
```
- Current hat size (0.6 - 2.0)
- Initialized from slider value (1.2 default)

```javascript
let libraryLoadPromise = null;
```
- Caches library loading promise
- Prevents multiple simultaneous load attempts

```javascript
const loadedScripts = new Set();
```
- Tracks successfully loaded script URLs
- Prevents duplicate script tags

### Core Functions

#### Script Loading

```javascript
function loadScriptOnce(url) {
  if (loadedScripts.has(url)) {
    return Promise.resolve();  // Already loaded
  }
  
  return new Promise((resolve, reject) => {
    const script = document.createElement('script');
    script.src = url;
    script.async = true;
    
    script.onload = () => {
      loadedScripts.add(url);  // Mark as loaded
      resolve();
    };
    
    script.onerror = () => {
      script.remove();  // Clean up failed script
      reject(new Error('Failed to load ' + url));
    };
    
    document.head.appendChild(script);
  });
}
```
**Purpose**: Dynamically load JavaScript files
- Creates `<script>` tag programmatically
- Returns Promise for async handling
- Prevents duplicate loads with Set
- Cleans up on error

```javascript
async function loadWithFallback(urls, label) {
  let lastError = null;
  
  for (const url of urls) {
    try {
      await loadScriptOnce(url);
      return;  // Success! Exit function
    } catch (error) {
      lastError = error;
      console.warn(`Failed to load ${label} from ${url}`, error);
    }
  }
  
  // All URLs failed
  throw new Error(`Failed to load ${label}. Last error: ${lastError.message}`);
}
```
**Purpose**: Try multiple CDN sources
- Loops through URL array
- Returns on first success
- Throws only if all fail

#### Math Utilities

```javascript
function lerp(a, b, t) {
  return a + (b - a) * t;
}
```
**Purpose**: Linear interpolation (smoothing)
- `a`: Current value
- `b`: Target value
- `t`: Interpolation factor (0-1)
- Example: `lerp(100, 200, 0.25) = 125`

```javascript
function mirrorX(x, width) {
  return (1 - x) * width;
}
```
**Purpose**: Mirror X coordinate for video flip
- MediaPipe returns normalized coords (0-1)
- Flips horizontal position
- Scales to pixel width

#### Face Tracking

```javascript
function computePose(landmarks) {
  const width = stage.clientWidth;
  const height = stage.clientHeight;
  
  // Extract key landmarks
  const top = landmarks[10];      // Forehead center
  const left = landmarks[234];    // Left temple
  const right = landmarks[454];   // Right temple
  
  // Convert to pixel coordinates (mirrored)
  const leftX = mirrorX(left.x, width);
  const leftY = left.y * height;
  const rightX = mirrorX(right.x, width);
  const rightY = right.y * height;
  const topX = mirrorX(top.x, width);
  const topY = top.y * height;
  
  // Calculate head width using distance formula
  const headWidth = Math.hypot(rightX - leftX, rightY - leftY);
  
  // Scale hat proportionally
  const hatWidth = headWidth * 1.55 * sizeMultiplier;
  const hatHeight = hatWidth * HAT_ASPECT;
  
  // Position hat above forehead
  const centerX = topX;
  const centerY = topY - hatHeight * 0.55;
  
  return { x: centerX, y: centerY, width: hatWidth };
}
```
**Purpose**: Calculate hat position from face landmarks

**Landmark Selection**:
- **10**: Top of forehead (vertical reference)
- **234**: Left temple (width calculation)
- **454**: Right temple (width calculation)

**Sizing Formula**:
- `headWidth * 1.55`: Hat ~155% of head width
- `* sizeMultiplier`: User adjustment (0.6 - 2.0)
- `* HAT_ASPECT`: Maintain proportions

**Positioning**:
- X: Centered on forehead
- Y: Above head by 55% of hat height

```javascript
function applyPose(pose) {
  hat.style.left = `${pose.x}px`;
  hat.style.top = `${pose.y}px`;
  hat.style.width = `${pose.width}px`;
  hat.style.opacity = 1;
  hat.style.transform = 'translate(-50%, -50%)';
  
  if (hatName.textContent) {
    const fontSize = Math.max(14, Math.min(36, pose.width * 0.12));
    hatName.style.fontSize = `${fontSize}px`;
    hatName.style.opacity = 1;
  }
}
```
**Purpose**: Apply calculated position to DOM
- Uses CSS `left/top` for positioning
- `transform: translate(-50%, -50%)` centers on point
- Dynamic font sizing: 12% of hat width (14px - 36px)

```javascript
function onResults(results) {
  const face = results.multiFaceLandmarks && results.multiFaceLandmarks[0];
  
  if (!trackingActive) return;
  
  if (!face) {
    const now = performance.now();
    if (now - lastFaceTs > 800) {
      setStatus(getStatusMessage('statusLookingForFace'), 'warn');
    }
    fadeHat();
    return;
  }
  
  lastFaceTs = performance.now();
  setStatus(getStatusMessage('statusHatLocked'), 'ready');
  
  const targetPose = computePose(face);
  if (!targetPose) {
    fadeHat();
    return;
  }
  
  if (!smoothedPose) {
    smoothedPose = targetPose;  // First frame: snap to position
  } else {
    // Apply smoothing
    smoothedPose = {
      x: lerp(smoothedPose.x, targetPose.x, SMOOTHING),
      y: lerp(smoothedPose.y, targetPose.y, SMOOTHING),
      width: lerp(smoothedPose.width, targetPose.width, SMOOTHING)
    };
  }
  
  applyPose(smoothedPose);
}
```
**Purpose**: MediaPipe callback for each frame
- Called ~30 times per second
- Extracts first detected face
- Handles no-face scenario (800ms delay before warning)
- Applies motion smoothing with lerp
- Updates hat position

#### Camera Control

```javascript
async function start() {
  if (trackingActive) return;  // Prevent double-start
  
  setStatus(getStatusMessage('statusPreparing'));
  
  // Check browser support
  if (!navigator.mediaDevices?.getUserMedia) {
    setStatus('Camera API not supported in this browser.', 'error');
    return;
  }
  
  setStartLoading(true);
  startBtn.disabled = true;
  
  try {
    // Load libraries
    setStatus(getStatusMessage('statusLoading'));
    await ensureLibrariesLoaded();
    
    // Check if libraries loaded successfully
    const FaceMeshCtor = getFaceMeshCtor();
    if (!FaceMeshCtor) {
      setStatus('FaceMesh library unavailable. Refresh the page.', 'error');
      return;
    }
    
    const CameraCtor = getCameraCtor();
    if (!CameraCtor) {
      setStatus('Camera utils script did not load.', 'error');
      return;
    }
    
    // Initialize FaceMesh
    faceMesh = new FaceMeshCtor({
      locateFile: (file) => {
        // Use local files if available
        if (loadedScripts.has('libs/face_mesh.js') && 
            LOCAL_FACE_MESH_ASSETS.has(file)) {
          return `libs/${file}`;
        }
        return `https://cdn.jsdelivr.net/npm/@mediapipe/face_mesh/${file}`;
      }
    });
    
    // Configure face detection
    faceMesh.setOptions({
      maxNumFaces: 1,                    // Track single face
      refineLandmarks: true,             // More accurate landmarks
      minDetectionConfidence: 0.6,       // Detection threshold
      minTrackingConfidence: 0.6         // Tracking threshold
    });
    
    faceMesh.onResults(onResults);
    
    // Initialize camera
    camera = new CameraCtor(video, {
      onFrame: async () => {
        await faceMesh.send({ image: video });
      },
      width: 1280,
      height: 720
    });
    
    setStatus(getStatusMessage('statusStartingCamera'));
    await camera.start();
    
    // Update UI
    stagePlaceholder.classList.add('hidden');
    setStartLoading(false);
    trackingActive = true;
    stopBtn.disabled = false;
    setStatus(getStatusMessage('statusMoveIntoView'), 'ready');
    
  } catch (error) {
    setStatus('Camera setup failed: ' + (error?.message || String(error)), 'error');
    stop({ silent: true });
  } finally {
    if (!trackingActive) {
      startBtn.disabled = false;
      setStartLoading(false);
    }
  }
}
```
**Purpose**: Initialize and start face tracking
- Validates browser support
- Loads MediaPipe libraries with fallbacks
- Configures face detection parameters
- Starts camera at 1280x720
- Handles errors gracefully

```javascript
function stop(options = {}) {
  const silent = options.silent === true;
  trackingActive = false;
  
  // Stop camera stream
  if (camera) {
    camera.stop();
    camera = null;
  }
  
  // Release video tracks
  if (video.srcObject) {
    for (const track of video.srcObject.getTracks()) {
      track.stop();
    }
    video.srcObject = null;
  }
  
  // Clean up FaceMesh
  if (faceMesh && typeof faceMesh.close === 'function') {
    faceMesh.close();
  }
  faceMesh = null;
  
  // Reset state
  smoothedPose = null;
  fadeHat();
  stopBtn.disabled = true;
  startBtn.disabled = false;
  setStartLoading(false);
  stagePlaceholder.classList.remove('hidden');
  
  if (!silent) {
    setStatus(getStatusMessage('statusIdle'), 'idle');
  }
}
```
**Purpose**: Stop camera and release resources
- Stops MediaPipe processing
- Releases camera hardware
- Cleans up memory
- Resets UI state
- `silent` option skips status message

#### UI Functions

```javascript
function setStatus(message, tone = 'info') {
  if (!statusEl || !statusPill) return;
  
  const trimmed = message ? message.trim() : '';
  let fallback = getStatusMessage('statusWorking');
  
  if (tone === 'ready') fallback = getStatusMessage('statusAllSet');
  else if (tone === 'idle') fallback = getStatusMessage('waitingToBegin');
  else if (tone === 'error') fallback = getStatusMessage('statusActionNeeded');
  else if (tone === 'warn') fallback = getStatusMessage('statusHoldSteady');
  
  statusPill.textContent = trimmed || fallback;
  statusPill.classList.remove('ready', 'alert', 'idle', 'warn');
  
  if (tone === 'ready') statusPill.classList.add('ready');
  else if (tone === 'error') statusPill.classList.add('alert');
  else if (tone === 'idle') statusPill.classList.add('idle');
  else if (tone === 'warn') statusPill.classList.add('warn');
  
  statusEl.hidden = false;
}
```
**Purpose**: Update status indicator with color coding
- `ready` → green (face detected)
- `error` → red (critical issue)
- `warn` → yellow (no face detected)
- `idle` → gray (waiting to start)

```javascript
function toggleFullscreen() {
  if (!document.fullscreenElement && 
      !document.webkitFullscreenElement && 
      !document.mozFullScreenElement) {
    // Enter fullscreen
    const elem = stage;
    if (elem.requestFullscreen) {
      elem.requestFullscreen();
    } else if (elem.webkitRequestFullscreen) {
      elem.webkitRequestFullscreen();  // Safari
    } else if (elem.mozRequestFullScreen) {
      elem.mozRequestFullScreen();  // Firefox
    } else if (elem.msRequestFullscreen) {
      elem.msRequestFullscreen();  // IE11
    }
  } else {
    // Exit fullscreen
    if (document.exitFullscreen) {
      document.exitFullscreen();
    } else if (document.webkitExitFullscreen) {
      document.webkitExitFullscreen();
    } else if (document.mozCancelFullScreen) {
      document.mozCancelFullScreen();
    } else if (document.msExitFullscreen) {
      document.msExitFullscreen();
    }
  }
}
```
**Purpose**: Cross-browser fullscreen toggle
- Checks for vendor-prefixed properties
- Works on Chrome, Firefox, Safari, IE11

---

## Face Tracking Algorithm

### MediaPipe Face Mesh

MediaPipe detects **468 3D landmarks** on the face:

```
Landmark Index Map:
0-16:    Face oval
17-226:  Left eye region
227-407: Right eye region
409-467: Lips and mouth
10:      Forehead center (TOP)
234:     Left temple
454:     Right temple
```

### Coordinate System

MediaPipe returns normalized coordinates:
- X: 0.0 (left) to 1.0 (right)
- Y: 0.0 (top) to 1.0 (bottom)
- Z: Depth (not used in this app)

### Position Calculation Steps

1. **Get key landmarks**
```javascript
const top = landmarks[10];    // {x: 0.5, y: 0.2, z: 0.1}
const left = landmarks[234];   // {x: 0.3, y: 0.4, z: 0.0}
const right = landmarks[454];  // {x: 0.7, y: 0.4, z: 0.0}
```

2. **Convert to pixels**
```javascript
const leftX = (1 - 0.3) * 640 = 448px   // Mirrored
const leftY = 0.4 * 480 = 192px
const rightX = (1 - 0.7) * 640 = 192px
const rightY = 0.4 * 480 = 192px
```

3. **Calculate head width**
```javascript
const dx = rightX - leftX = -256
const dy = rightY - leftY = 0
const headWidth = √(dx² + dy²) = 256px
```

4. **Scale hat**
```javascript
const hatWidth = 256 * 1.55 * 1.2 = 476px
const hatHeight = 476 * (380/600) = 302px
```

5. **Position hat**
```javascript
const hatX = (1 - 0.5) * 640 = 320px   // Center X
const hatY = 0.2 * 480 - (302 * 0.55) = 96 - 166 = -70px
```

### Motion Smoothing

Without smoothing, hat would jump around due to detection noise.

**Lerp (Linear Interpolation)**:
```javascript
newX = oldX + (targetX - oldX) * 0.25

// Example:
// Frame 1: oldX = 100, targetX = 200
// newX = 100 + (200-100)*0.25 = 125

// Frame 2: oldX = 125, targetX = 200
// newX = 125 + (200-125)*0.25 = 143.75

// Frame 3: oldX = 143.75, targetX = 200
// newX = 143.75 + (200-143.75)*0.25 = 157.8125
```

Gradually approaches target without sudden jumps.

---

## Translation System

### Structure

```javascript
const translations = {
  en: {
    key: "English text",
    ...
  },
  ar: {
    key: "النص العربي",
    ...
  }
};
```

### Translation Markers

**HTML Text Content**:
```html
<h1 data-i18n="title">Default Text</h1>
```

**Input Placeholders**:
```html
<input data-i18n-placeholder="namePlaceholder" />
```

**Select Options**:
```html
<option data-i18n-option="hatPolice">Police</option>
```

### Update Process

```javascript
function setLanguage(lang) {
  // 1. Update HTML attributes
  document.documentElement.setAttribute('lang', lang);
  document.documentElement.setAttribute('dir', lang === 'ar' ? 'rtl' : 'ltr');
  
  // 2. Update text content
  document.querySelectorAll('[data-i18n]').forEach(element => {
    const key = element.getAttribute('data-i18n');
    element.textContent = translations[lang][key];
  });
  
  // 3. Update placeholders
  document.querySelectorAll('[data-i18n-placeholder]').forEach(input => {
    const key = input.getAttribute('data-i18n-placeholder');
    input.placeholder = translations[lang][key];
  });
  
  // 4. Update options
  document.querySelectorAll('option[data-i18n-option]').forEach(option => {
    const key = option.getAttribute('data-i18n-option');
    option.textContent = translations[lang][key];
  });
  
  // 5. Update page title
  document.title = translations[lang].title;
}
```

### Dynamic Messages

For messages generated in JavaScript:
```javascript
function getStatusMessage(key) {
  return translations[currentLanguage][key] || key;
}

// Usage:
setStatus(getStatusMessage('statusHatLocked'), 'ready');
```

---

## Configuration Guide

### Changing Hat Size Formula

In `computePose()` function:
```javascript
// Current: Hat is 155% of head width
const hatWidth = headWidth * 1.55 * sizeMultiplier;

// Make hat bigger:
const hatWidth = headWidth * 2.0 * sizeMultiplier;

// Make hat smaller:
const hatWidth = headWidth * 1.2 * sizeMultiplier;
```

### Adjusting Vertical Position

In `computePose()` function:
```javascript
// Current: Hat is 55% of its height above forehead
const centerY = topY - hatHeight * 0.55;

// Lower hat (closer to head):
const centerY = topY - hatHeight * 0.3;

// Raise hat (further from head):
const centerY = topY - hatHeight * 0.8;
```

### Changing Smoothing

In constants section:
```javascript
// Current: 25% responsiveness
const SMOOTHING = 0.25;

// More responsive (but jittery):
const SMOOTHING = 0.5;

// Smoother (but laggy):
const SMOOTHING = 0.1;

// No smoothing (instant, but jittery):
const SMOOTHING = 1.0;
```

### Camera Resolution

In `start()` function:
```javascript
camera = new CameraCtor(video, {
  onFrame: async () => { ... },
  width: 1280,   // Change to 640 for lower quality/better performance
  height: 720    // Change to 480 for lower quality/better performance
});
```

### Face Detection Sensitivity

In `start()` function:
```javascript
faceMesh.setOptions({
  maxNumFaces: 1,
  refineLandmarks: true,
  minDetectionConfidence: 0.6,  // Lower = easier to detect (0.1 - 1.0)
  minTrackingConfidence: 0.6    // Lower = tracks through more occlusion
});
```

### Adding New SVG Hats

1. **Create SVG** with viewBox="0 0 600 380":
```html
<svg id="hat-custom" class="hat-svg" xmlns="http://www.w3.org/2000/svg" 
     viewBox="0 0 600 380" style="display: none;">
  <!-- Your paths here -->
  <circle cx="300" cy="190" r="100" fill="red"/>
</svg>
```

2. **Add to dropdown**:
```html
<option value="custom" data-i18n-option="hatCustom">Custom Hat</option>
```

3. **Add translations**:
```javascript
en: { hatCustom: 'Custom Hat', ... }
ar: { hatCustom: 'قبعة مخصصة', ... }
```

4. **SVG Design Tips**:
   - Center design around x=300
   - Keep important elements within safe area
   - Use gradients for depth
   - Test with different sizes

---

## Performance Optimization

### Current Optimizations

1. **Script Caching**: `loadedScripts` Set prevents duplicate loads
2. **Single Face Tracking**: `maxNumFaces: 1` reduces processing
3. **GPU Acceleration**: CSS transforms use GPU
4. **Efficient Selectors**: Cache DOM queries in variables
5. **Conditional Processing**: Check `trackingActive` early
6. **Debounced Status Updates**: 800ms delay before "looking for face"

### Further Optimization Ideas

1. **Reduce Resolution**:
```javascript
width: 640,  // Instead of 1280
height: 480  // Instead of 720
```

2. **Lower Frame Rate**:
```javascript
// Skip every other frame
let frameCount = 0;
onFrame: async () => {
  if (++frameCount % 2 === 0) {
    await faceMesh.send({ image: video });
  }
}
```

3. **Simplified Landmarks**:
```javascript
refineLandmarks: false  // Faster but less accurate
```

4. **requestAnimationFrame for Rendering**:
```javascript
function render() {
  if (smoothedPose && trackingActive) {
    applyPose(smoothedPose);
  }
  requestAnimationFrame(render);
}
```

---

## Security Considerations

### Camera Privacy

- Camera only accessible via HTTPS (browser requirement)
- Video never leaves user's device
- No recording or transmission
- User must explicitly grant permission

### XSS Prevention

- No `eval()` or `innerHTML` used
- All content set via `textContent` (escapes HTML)
- User input sanitized before display

### Content Security Policy (Recommended)

Add to HTML head:
```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self' 'unsafe-inline' https://cdn.jsdelivr.net https://unpkg.com https://fonts.googleapis.com;
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  font-src https://fonts.gstatic.com;
  media-src 'self' blob:;
  connect-src 'self' https://cdn.jsdelivr.net https://unpkg.com;
">
```

---

## Testing Checklist

### Functional Tests

- [ ] Camera starts successfully
- [ ] Face detection works in good lighting
- [ ] Hat follows face movements smoothly
- [ ] All 5 hat types display correctly
- [ ] Custom text appears on hat
- [ ] Size slider adjusts hat size
- [ ] Recenter button resets position
- [ ] Fullscreen mode works
- [ ] Language switch updates all text
- [ ] RTL layout correct in Arabic
- [ ] Stop button ends session

### Browser Tests

- [ ] Chrome (Windows/Mac/Linux)
- [ ] Firefox (Windows/Mac/Linux)
- [ ] Safari (Mac/iOS)
- [ ] Edge (Windows)
- [ ] Mobile Chrome (Android)
- [ ] Mobile Safari (iOS)

### Edge Cases

- [ ] No camera available
- [ ] Camera permission denied
- [ ] Poor lighting conditions
- [ ] Multiple faces in frame
- [ ] Face partially out of frame
- [ ] Very close to camera
- [ ] Very far from camera
- [ ] Slow network (CDN fallbacks)
- [ ] Offline mode (local libs)

---

## Debugging Guide

### Common Console Messages

**"Failed to load X from Y"**
- Normal if trying CDN fallbacks
- Only concerning if all URLs fail

**"Camera API not supported"**
- Browser too old or not using HTTPS
- Mobile browsers require HTTPS

**"TypeError: Cannot read property 'x' of undefined"**
- Landmark not detected
- Check lighting and face visibility

### Enable Verbose Logging

Add to `onResults()`:
```javascript
function onResults(results) {
  console.log('Faces detected:', results.multiFaceLandmarks?.length || 0);
  if (results.multiFaceLandmarks?.[0]) {
    console.log('Landmarks:', results.multiFaceLandmarks[0].length);
  }
  // ... rest of function
}
```

### Performance Monitoring

Add FPS counter:
```javascript
let lastTime = performance.now();
let frames = 0;

function onResults(results) {
  frames++;
  const now = performance.now();
  if (now - lastTime >= 1000) {
    console.log('FPS:', frames);
    frames = 0;
    lastTime = now;
  }
  // ... rest of function
}
```

---

This documentation covers all aspects of the Hat Filter Studio codebase. For specific implementation details, refer to the inline comments in `index.html`.
