# RoadGuard AI — Detect. Report. Repair. 🛡️🚧

> An AI-powered, mobile-first civic-tech platform that empowers citizens to report potholes and hazardous road defects using their smartphone camera and verified GPS coordinates, while providing municipal road maintenance authorities with automated severity triage, crew dispatching, and citizen status notifications.

---

## 📱 Mobile-First Features & Architecture

* **Real Mobile Camera Experience**:
  * Live camera stream using HTML5 `MediaDevices API` (`facingMode: "environment"`).
  * On-screen targeting reticle HUD for pothole centering.
  * Camera flip (rear/front) and flashlight/torch support.
  * Native device camera fallback (`<input capture="environment">`) for maximum mobile browser compatibility.
  * Instant photo review with **Retake** and **Continue** actions. Absolutely zero fake stock photos substituted.
* **Automatic GPS Acquisition**:
  * High-accuracy browser `Geolocation API` obtaining latitude, longitude, and accuracy in meters.
  * Reverse geocoding via OpenStreetMap Nominatim for street/area display.
  * Live acquisition indicator with clear permissions guidance and retry fallback.
* **AI-Assisted Damage Detection**:
  * Edge computer vision image analysis running directly in the browser.
  * Samples cavity darkness, luminance variance, and surface fracture metrics.
  * Classifies into: `Pothole`, `Cracked Road`, `Broken Surface`, `Damaged Manhole`, or `Other`.
  * Computes damage severity (`Critical`, `High`, `Medium`, `Low`), confidence score, and hazard risk assessment.
  * Modular design ready to connect to external YOLOv8 or CV models via `VITE_AI_MODEL_ENDPOINT`.
* **Geo-Proximity Duplicate Detection**:
  * Haversine formula calculation alerts citizens if an existing complaint is detected within 75 meters.
  * Allows citizens to view the nearby report or continue submitting.
* **Free Interactive Map (No Google Maps API Key)**:
  * Built with **Leaflet.js** and **OpenStreetMap** tiles with full attribution.
  * Custom retina hazard pins color-coded by severity (pulsing red for critical hazards, green for resolved).
  * User live location indicator with pulsing blue radar dot.
  * Interactive marker popups with photo thumbnails, status badges, and quick tracking links.
* **Authority Road Maintenance Dashboard**:
  * Protected supervisor view for road works personnel.
  * KPI statistics: Total Reports, Critical Hazards, Active Crews, and Resolution Rate %.
  * Search by complaint ID or street name, multi-filter by severity and category.
  * Status dispatch workflow: `SUBMITTED` ➔ `UNDER REVIEW` ➔ `ASSIGNED` ➔ `IN PROGRESS` ➔ `RESOLVED`.
  * Assigns maintenance squads (e.g. *Metro Rapid Paving Squad #1*, *Heavy Asphalt Resurfacing Unit B*, *Utility & Drainage Crew*).
  * Automatically updates the citizen's progress timeline and fires in-app notifications.
* **Unified Dual-Layer Backend (Supabase + Demo Mode)**:
  * Complete Supabase PostgreSQL schema provided in `database/schema.sql`.
  * Self-contained demonstration mode with persistent local storage when Supabase credentials are not configured.

---

## 🚀 Quick Start & Local Development

### 1. Prerequisites
* Node.js v20+ or v22+
* npm

### 2. Installation
```bash
# Navigate to the project directory
cd roadguard-ai

# Install dependencies
npm install
```

### 3. Running the Development Server
```bash
npm run dev
```
Open your browser at `http://localhost:5173` (or the URL shown in terminal).

### 4. Building for Production
```bash
npm run build
npm run preview
```

---

## 🗄️ Database & Cloud Storage Setup (Supabase)

RoadGuard AI is pre-configured with a full PostgreSQL schema located at [`database/schema.sql`](database/schema.sql).

### Steps to connect your Supabase project:
1. Create a project at [supabase.com](https://supabase.com).
2. Go to the **SQL Editor** tab in your Supabase Dashboard.
3. Paste and run the contents of [`database/schema.sql`](database/schema.sql).
4. Go to **Storage**, create a public bucket named `road-damage-photos`.
5. Copy your project URL and public Anon Key from **Settings ➔ API**.
6. Create a `.env` file in the project root:
   ```env
   VITE_SUPABASE_URL=https://your-project.supabase.co
   VITE_SUPABASE_ANON_KEY=your-anon-key
   ```
7. Rebuild or restart `npm run dev`. RoadGuard will automatically switch to cloud sync mode!

---

## 🧪 Testing Checklist

1. **TEST 1 (Mobile Camera Launch)**:
   * Click the prominent "REPORT ROAD DAMAGE" button or bottom center camera button.
   * Verify that the camera viewfinder opens or device camera prompt appears.
2. **TEST 2 (Photo Capture & Preview)**:
   * Capture a real photograph of a road surface.
   * Verify that the real photo appears immediately in the preview screen with Retake and Continue buttons.
3. **TEST 3 (GPS Acquisition)**:
   * Tap Continue; verify that the GPS indicator pulses and obtains latitude, longitude, and accuracy in meters.
4. **TEST 4 (Location Pin & Mini Map)**:
   * Verify that the mini map correctly renders the obtained coordinates.
5. **TEST 5 (Report Submission & Unique ID)**:
   * Review AI damage analysis, enter an optional note, and tap "Submit Complaint".
   * Verify that confetti triggers and a unique ID (e.g., `RG-100006`) is generated.
6. **TEST 6 (Photo + Location Storage)**:
   * Tap "Track This Complaint"; verify that the captured photo, GPS coordinates, and timeline are preserved together.
7. **TEST 7 (My Reports User Isolation)**:
   * Open the "My Reports" tab. Verify that complaints submitted under the current citizen user are listed with their status timeline.
8. **TEST 8 (Authority Dispatch & Status Update)**:
   * Tap the role switcher in the header to switch to "Authority Mode".
   * Open the Admin tab, locate the complaint, click "Update Status & Crew", assign a crew and change status to `IN_PROGRESS` or `RESOLVED`.
   * Switch back to "Citizen Mode"; verify that the notification bell rings and the progress timeline updates to reflect the new status!
9. **TEST 9 (Error & Permission Handling)**:
   * Test denying camera or location permission; verify friendly instructional notices and retry buttons appear.
10. **TEST 10 (Interactive Map & Popups)**:
    * Open the "Live Map" tab. Tap any pin to see the photo thumbnail, severity, status, and track button. Test the "My Location" recenter button.
