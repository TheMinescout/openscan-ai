# 🔍 OpenScan-AI: The One-Shot Coding Battle

A web-based document and object scanning application built to compare the architectural and creative capabilities of the world's most advanced AI models. 

## ⚔️ The Challenge
Each AI model was given a single, complex prompt to create a fully functional, single-file HTML5 scanning app. No human bug fixes, no follow-up prompts, and no external assets allowed (other than CDNs).

### The Contenders:
* **Gemini 3.0 Thinking** (Google)
* **GPT-5** (OpenAI)
* **Claude 4.6 Extended** (Anthropic) - *Awaiting Results*

## 🚀 Features (Targeted)
* **Live Viewfinder:** Real-time camera stream with scanning overlays.
* **Glassmorphism UI:** A sleek, modern dark-themed interface.
* **OCR Integration:** Text recognition powered by Tesseract.js.
* **Image Processing:** High-contrast filters for document clarity.
* **Exporting:** PDF generation and clipboard support.

## 📂 Repository Structure
* `/gemini.html`: The raw output from Gemini 3.0 Thinking.
* `/gpt.html`: The raw output from GPT-5.
* `/claude.html`: The raw output from Claude 4.6 (Coming Soon).
* `/index.html`: The "Perfected Version" (Human/AI Synergy).

---

# 🏆 The Perfected Build: `/index.html`

While the raw AI files demonstrate the baseline capabilities of LLMs in a "one-shot" environment, `/index.html` represents the **Ultimate Edge Application**. Through iterative development, we abandoned heavy, battery-draining libraries (like Tesseract.js and OpenCV) and engineered a purely serverless, offline-first, mobile-native scanner that runs entirely in the browser.

Here is a technical deep-dive into the custom engines powering the `index.html` app.

### 1. Motion-Stability Autoscan Engine
True "autoscan" usually requires heavy computer vision libraries to detect edges, which can crash mobile browsers and drain batteries. To solve this, Openscan AI uses a highly optimized **Motion-Stability Algorithm**:
* **The Process:** While the camera is active, a hidden `<canvas>` continuously downsamples the video feed to a tiny 64x64 pixel grid at 250ms intervals.
* **Math in Action:** It compares the RGB values of the current frame against the previous frame. If the average pixel difference drops below a specific threshold (`< 8`), the engine knows the user is holding the phone perfectly still.
* **The Trigger:** After 1.25 seconds of stability, the UI guide flashes green, the photo is automatically captured, and a 3-second cooldown is triggered to prevent duplicate scans.

### 2. Aspect-Ratio Aware Auto-Cropping
Taking a photo is easy; cropping it accurately based on an arbitrary on-screen CSS box is notoriously difficult. 
* The app calculates the exact aspect ratio of the raw `MediaStream` video feed compared to the `object-fit: cover` boundaries of the user's screen.
* When the user hits capture, the engine maps the `getBoundingClientRect` DOM coordinates of the white scanning guide back to the raw, unscaled video pixels.
* It uses `CanvasRenderingContext2D.drawImage()` with 9 parameters to instantly slice out the precise document boundaries, discarding the rest of the image before saving.

### 3. "Bring Your Own Key" (BYOK) Cloud Architecture
Because GitHub Pages relies on 100% static hosting, Openscan AI has no centralized database or backend server. It relies entirely on the client's browser.
* **Offline-First Core:** All scans are instantly written to `IndexedDB` via `localForage`. The app works flawlessly in airplane mode.
* **Google Drive Sync:** Users can securely authenticate using the **Google Identity Services (GSI) OAuth 2.0** flow. Scans are beamed directly into a hidden `.openscan` folder in their personal Google Drive.
* **Developer S3/R2 Mode:** A hidden developer toggle allows power users to input their own S3 API credentials to back up scans directly to a Cloudflare R2 bucket.

### 4. AI PDF Layout Generation (`@cf/meta/llama-3-8b-instruct`)
Instead of hardcoding PDF layouts, the app outsources layout design to a Large Language Model.
* Users input their **Cloudflare API Token** into the secure local settings.
* When exporting, the app bundles the Base64 images alongside the user's text instructions (e.g., *"Make this look like a sleek, two-column invoice"*).
* This payload is sent to Cloudflare Workers AI, where **Llama-3** generates raw, styled HTML.
* The resulting HTML is injected into the DOM and seamlessly converted into a downloadable document using `html2pdf.js`.

### 5. Linear/Notion-Inspired UI System
The UI was meticulously crafted to feel like a native iOS/Android application, completely ditching the "web app" feel.
* **Mobile-First UX:** Prevents rubber-band body scrolling, features a fixed bottom navigation bar on mobile, and moves to a persistent sidebar on desktop.
* **Live Video Filtering:** Instead of applying filters *after* taking the photo, the app applies dynamic CSS `filter` matrices (`grayscale()`, `contrast()`, `saturate()`) directly to the `<video>` element, allowing users to see the enhanced document in real-time.
* **Dynamic Selection Bar:** A smooth, slide-up action bar for bulk deletion and bulk exporting (including a `JSZip` implementation for downloading entire scan libraries at once).

## ⚖️ License
MIT License - Feel free to use this for your own experiments!
