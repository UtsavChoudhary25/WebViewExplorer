# WebView Explorer 🌐

[![Android](https://img.shields.io/badge/Platform-Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)](https://developer.android.com)
[![Language](https://img.shields.io/badge/Language-Java-007396?style=for-the-badge&logo=java&logoColor=white)](https://www.java.com)
[![Min SDK](https://img.shields.io/badge/Min%20SDK-24%20(Android%207.0)-blue?style=for-the-badge)](https://developer.android.com/about/versions/nougat)
[![Target SDK](https://img.shields.io/badge/Target%20SDK-37-brightgreen?style=for-the-badge)](https://developer.android.com)
[![License](https://img.shields.io/badge/License-MIT-orange?style=for-the-badge)](LICENSE)

**WebView Explorer** is a feature-complete Android web browser application developed in Java. It demonstrates how to embed and control web content inside an Android app using Android's native `WebView` component, customized with a `WebViewClient` for navigation and error handling, and a `WebChromeClient` for real-time progress updates.

---

## 🎓 Academic Profile & Project Info

| Attribute | Details |
| :--- | :--- |
| **Student Name** | **Utsav Choudhary** |
| **Course** | Master of Computer Applications (MCA) |
| **Experiment No.** | **Experiment 8** — Implement WebView in Android |
| **Development Language** | Java (JDK 11 / OpenJDK) |
| **Architecture / Framework** | AndroidX, Material Components, View Binding / Layout XML |

---

## 📱 Application Screenshots & UI Showcase

| 1. Google Home (Default Load) | 2. Browsing Page (Wikipedia) | 3. Error Handling State |
| :---------------------------: | :--------------------------: | :---------------------: |
| ![Google Home](screenshots/01_google_home.png) | ![Wikipedia Page](screenshots/02_wikipedia_page.png) | ![Error State](screenshots/03_error_state.png) |
| *Default home page with student card & search controls* | *Web browsing with dynamic navigation button feedback* | *Red error banner indicating invalid domain or network error* |

---

## 🚀 Key Features & Implementation Breakdown

### 1. 🌐 Embedded Web Rendering Engine
- **JavaScript Execution:** Enabled via `WebSettings.setJavaScriptEnabled(true)` to render interactive web applications like Google, Wikipedia, YouTube, and modern web portals.
- **Built-in Zoom Controls:** Configured with `setBuiltInZoomControls(true)` and `setDisplayZoomControls(false)` for pinch-to-zoom capabilities without obstructive screen buttons.

### 2. 🔀 Navigation Controls & Dynamic UI Feedback
- **History Back Navigation (◄):** Navigates to the previous page in history using `webView.goBack()`.
- **History Forward Navigation (►):** Navigates forward in history using `webView.goForward()`.
- **Page Refresh (↻):** Reloads the current URL using `webView.reload()`.
- **Dynamic Button Alpha & Enabling:** The `Back` and `Forward` buttons dynamically update their enabled state and opacity (`1.0f` active vs `0.5f` disabled) based on `webView.canGoBack()` and `webView.canGoForward()`.

### 3. 🔍 Smart Address Bar & Input Validation
- **Protocol Auto-Completion:** Automatically prepends `https://` if the user enters a domain without a scheme (e.g., converting `google.com` to `https://google.com`).
- **Regex URL Matching:** Uses Android's native `Patterns.WEB_URL` matcher to ensure valid domain formatting before attempting navigation.
- **Keyboard IME Integration:** Listens for `EditorInfo.IME_ACTION_GO` to trigger page load directly from the soft keyboard.
- **Automatic Soft Keyboard Dismissal:** Hides the input method manager (`InputMethodManager`) upon submitting a URL to maximize viewable screen space.

### 4. 📊 Progress Bar & Loading Indicator
- Uses `WebChromeClient#onProgressChanged(WebView view, int newProgress)` to track load progress from `0` to `100%`.
- Displays a horizontal `ProgressBar` during loading and automatically hides it when page load completes.

### 5. ⚠️ Robust Network & Page Error Handling
- Intercepts main-frame network errors via `WebViewClient#onReceivedError()`.
- Hides the `WebView` container and displays a clear red error message banner (`tvError`) notifying the user of DNS failures or network connectivity issues (`net::ERR_NAME_NOT_RESOLVED`).

### 6. 🔄 Lifecycle & State Preservation
- **Rotation Survival:** Overrides `onSaveInstanceState()` and `onRestoreInstanceState()` using `webView.saveState()` and `webView.restoreState()` to preserve page history and current state across device orientation changes.
- **Hardware Back Button Interception:** Overrides `onBackPressed()` to navigate back in web browser history first before closing the app.

---

## 🏗️ Technical Architecture & Code Overview

### Layout Architecture (`activity_main.xml`)
The main user interface uses a vertical `LinearLayout` containing:
1. **Student Info Header Card (`#E3F2FD`):** Displaying student metadata, USN, course, and experiment information.
2. **Control & Address Bar:** Horizontal bar containing Back, Forward, Reload `ImageButton` elements, an `EditText` URL bar, and a `Load` `Button`.
3. **Horizontal Progress Indicator:** `ProgressBar` showing real-time loading percentage.
4. **Error Message View:** `TextView` formatted with red text for network/URL errors.
5. **WebView Container:** Main viewport occupying remaining vertical screen space (`layout_weight="1"`).

### Core Logic Snippet (`MainActivity.java`)

```java
// WebView Settings & Client Callbacks Configuration
private void setupWebView() {
    WebSettings webSettings = webView.getSettings();
    webSettings.setJavaScriptEnabled(true);
    webSettings.setBuiltInZoomControls(true);
    webSettings.setDisplayZoomControls(false);

    webView.setWebViewClient(new WebViewClient() {
        @Override
        public void onPageStarted(WebView view, String url, Bitmap favicon) {
            super.onPageStarted(view, url, favicon);
            progressBar.setVisibility(View.VISIBLE);
            tvError.setVisibility(View.GONE);
            etUrl.setText(url);
            updateNavigationButtons();
        }

        @Override
        public void onPageFinished(WebView view, String url) {
            super.onPageFinished(view, url);
            progressBar.setVisibility(View.GONE);
            updateNavigationButtons();
        }

        @Override
        public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error) {
            super.onReceivedError(view, request, error);
            if (request.isForMainFrame()) {
                showError("Error loading page: " + error.getDescription());
            }
        }
    });

    webView.setWebChromeClient(new WebChromeClient() {
        @Override
        public void onProgressChanged(WebView view, int newProgress) {
            super.onProgressChanged(view, newProgress);
            progressBar.setProgress(newProgress);
            if (newProgress == 100) {
                progressBar.setVisibility(View.GONE);
            }
        }
    });
}
```

---

## 📂 Repository Directory Structure

```text
WebViewExplorer/
├── app/
│   ├── build.gradle.kts           # Module-level Gradle configuration (Sdk 37, Java 11)
│   └── src/
│       ├── main/
│       │   ├── java/com/example/webviewexplorer/
│       │   │   └── MainActivity.java       # Core application logic & WebView controller
│       │   ├── res/
│       │   │   ├── layout/
│       │   │   │   └── activity_main.xml   # User interface layout
│       │   │   ├── values/
│       │   │   │   ├── colors.xml          # Color definitions
│       │   │   │   ├── strings.xml         # String resources
│       │   │   │   └── themes.xml          # Material3 application theme
│       │   │   └── mipmap-*/               # Application launcher icons
│       │   └── AndroidManifest.xml         # Manifest file declaring INTERNET permission
│       └── test/                           # Unit and Instrumentation tests
├── gradle/
│   ├── libs.versions.toml         # Version catalog for dependencies
│   └── wrapper/                    # Gradle wrapper configuration
├── screenshots/                    # Application screenshots for documentation
│   ├── 01_google_home.png
│   ├── 02_wikipedia_page.png
│   └── 03_error_state.png
├── build.gradle.kts                # Project-level Gradle build file
├── gradle.properties               # JVM memory and build settings
├── settings.gradle.kts             # Dependency resolution and module management
└── README.md                       # Complete project documentation
```

---

## 🛠️ Prerequisites & Technology Stack

- **Operating System:** Windows, macOS, or Linux
- **IDE:** Android Studio Studio Ladybug / Meerkat (2024.2+) or newer
- **JDK:** OpenJDK / Temurin 11 or 17
- **Android SDK:**
  - `compileSdk`: 37
  - `targetSdk`: 37
  - `minSdk`: 24 (Android 7.0 Nougat)
- **Key Dependencies:**
  - `androidx.appcompat:appcompat:1.8.0`
  - `com.google.android.material:material:1.14.0`
  - `androidx.constraintlayout:constraintlayout:2.2.2`

---

## ⚙️ How to Build and Run

### 1. Clone the Repository
```bash
git clone https://github.com/UtsavChoudhary25/WebViewExplorer.git
cd WebViewExplorer
```

### 2. Open in Android Studio
1. Launch **Android Studio**.
2. Click **File > Open** (or select **Open** on the Welcome screen).
3. Select the `WebViewExplorer` directory.
4. Allow Gradle sync to complete automatically.

### 3. Build & Deploy
- Connect an Android device with USB Debugging enabled, or launch an Android Virtual Device (AVD).
- Run the application via Android Studio or command line:
  ```bash
  # Build debug APK
  ./gradlew assembleDebug

  # Install and run on connected device
  ./gradlew installDebug
  ```

---

## 🧪 Testing & Verification Guide

| Scenario | Test Steps | Expected Behavior |
| :--- | :--- | :--- |
| **Initial Launch** | Launch app on device or emulator | Default URL (`https://www.google.com`) loads automatically, progress bar animates, and navigation buttons update. |
| **Custom URL Loading** | Enter `wikipedia.org` in URL box and tap **Load** or press **Go** on keyboard | URL updates to `https://wikipedia.org`, keyboard hides, page renders cleanly in WebView. |
| **Back / Forward History** | Load Page A, then Page B; tap **◄ Back**, then **► Forward** | Browser navigates backward to Page A and forward to Page B smoothly. |
| **Invalid / Offline URL** | Enter `invalid-domain-test.org` or turn off Wi-Fi/Data | Red error message banner displays (`Error loading page: net::ERR_NAME_NOT_RESOLVED`) and WebView is hidden. |
| **Screen Rotation** | Rotate device between Portrait and Landscape while viewing a webpage | Page state, scroll position, and loaded URL are restored without reloading from network. |

---

## 📄 License & Attribution

Distributed under the **MIT License**. Free to use for educational and academic purposes.

*Developed by **Utsav Choudhary** as part of MCA Experiment 8.*
