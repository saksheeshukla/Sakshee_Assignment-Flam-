# VisionEdge - Real-Time Edge Detection Android App

VisionEdge is a powerful Android application that demonstrates real-time computer vision using OpenCV. The app processes live camera feeds and applies Canny edge detection algorithms to visualize edges in real-time, making it perfect for learning computer vision concepts and exploring image processing on mobile devices.

## ✅ Features Implemented

- 🎥 **Real-Time Camera Feed** - Live camera preview using Camera2 API with TextureView
- 🔍 **Canny Edge Detection** - Real-time edge detection processing using OpenCV
- 🎨 **Dual View Modes**:
  - Raw camera feed viewer
  - Processed edge detection viewer
- ⚡ **High-Performance Processing** - Native C++ image processing via JNI
- 🎯 **OpenGL ES Rendering** - GPU-accelerated frame rendering
- 🔒 **Runtime Permissions** - Proper Camera permission handling for Android 6.0+
- 📱 **Material Design** - Modern UI with Material Components
- 🧠 **OpenCV Integration** - Full OpenCV 4.x Android SDK integration
- 📊 **Grayscale Conversion** - Automatic grayscale processing for edge detection
- 🔄 **Real-Time Processing** - Smooth frame processing at 15+ FPS

## 📱 Screenshots

<p align="center">
  <img src="images/Pro.jpg" width="300" alt="Raw Camera View"/>
  <img src="images/Edge.jpg" width="300" alt="Edge Detection View"/>
</p>
<p align="center">
  <i>Left: Raw camera feed | Right: Real-time edge detection</i>
</p>

## 🏗️ Architecture Overview

VisionEdge uses a multi-layer architecture combining Java, JNI, and OpenCV C++ for efficient real-time image processing:

```
┌─────────────────────────────────────────────────────────┐
│                    User Interface                        │
│              (Material Components - Java)                │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│              Camera Layer (Java)                         │
│         - Camera2 API for frame capture                  │
│         - TextureView for preview                        │
│         - CameraBridgeViewBase (OpenCV)                  │
└──────────────────────┬──────────────────────────────────┘
                       │
                       │ Frame Data (byte[])
                       │
┌──────────────────────▼──────────────────────────────────┐
│           JNI Bridge (Java ↔ C++)                       │
│    - Converts Java byte[] to cv::Mat                    │
│    - Calls native processing functions                  │
└──────────────────────┬──────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────┐
│        OpenCV Processing Layer (C++)                     │
│    - Grayscale conversion (cvtColor)                    │
│    - Canny edge detection (80, 200 thresholds)          │
│    - RGBA conversion for rendering                      │
└──────────────────────┬──────────────────────────────────┘
                       │
                       │ Processed Mat
                       │
┌──────────────────────▼──────────────────────────────────┐
│         Rendering Layer (OpenGL ES 2.0)                  │
│    - Texture mapping from processed frames              │
│    - GPU-accelerated rendering                          │
│    - Real-time display (15+ FPS)                        │
└──────────────────────────────────────────────────────────┘
```

### Frame Processing Flow

1. **Camera Capture** → Camera2 API captures frames from device camera
2. **Frame Callback** → `onCameraFrame()` receives RGBA Mat from CameraBridgeViewBase
3. **Processing** → OpenCV applies Canny edge detection:
   ```java
   Imgproc.Canny(inputMat, outputMat, 80, 200);
   ```
4. **Rendering** → Processed Mat returned to OpenCV's rendering pipeline
5. **Display** → Frame rendered to TextureView via OpenGL ES

### Key Components

**Java Layer:**
- `MainActivity.java` - App entry point, navigation between modes
- `RawActivity.java` - Displays raw camera feed without processing
- `ProActivity.java` - Applies Canny edge detection in real-time

**JNI Bridge:**
- Handles communication between Java and native C++ code
- Converts between Java byte arrays and OpenCV Mat objects
- Manages memory allocation/deallocation

**OpenCV C++ Layer:**
- Implements computer vision algorithms
- Processes frames using optimized C++ code
- Provides better performance than pure Java implementation

## 🛠️ Technical Stack

| Component | Technology |
|-----------|-----------|
| **Language** | Java |
| **Computer Vision** | OpenCV Android SDK 4.x |
| **Camera API** | Android Camera2 API |
| **Rendering** | OpenGL ES 2.0 |
| **UI Framework** | Material Components for Android |
| **Native Bridge** | JNI (Java Native Interface) |
| **Build System** | Gradle 7.0+ |
| **Min SDK** | Android 8.0 (API 26) |
| **Target SDK** | Android 12 (API 31) |

## 🚀 Setup Instructions

### Prerequisites

Ensure you have the following installed:

- **Android Studio** Arctic Fox (2020.3.1) or newer
- **Android NDK** r21 or higher
- **CMake** 3.10.2 or higher (for native builds)
- **Git** for version control
- **Android device** with camera or emulator with camera support

### Step 1: Clone the Repository

```bash
git clone https://github.com/yourusername/VisionEdge.git
cd VisionEdge
```

### Step 2: Install Android NDK

1. Open Android Studio
2. Go to **Tools → SDK Manager**
3. Navigate to **SDK Tools** tab
4. Check the following:
   - ✅ NDK (Side by side)
   - ✅ CMake
   - ✅ LLDB (for debugging native code)
5. Click **Apply** and wait for installation

### Step 3: Download and Configure OpenCV SDK

#### Download OpenCV

1. Visit [OpenCV Releases](https://opencv.org/releases/)
2. Download **OpenCV Android SDK 4.x** (latest version)
3. Extract the downloaded archive

#### Configure OpenCV in Project

**Option A: Using Module (Recommended)**

The project includes OpenCV as a module dependency:

```gradle
// In app/build.gradle
dependencies {
    implementation project(path: ':sdk')
}
```

1. Create `sdk` directory in project root
2. Copy OpenCV SDK contents to `sdk/` folder
3. Ensure `sdk/build.gradle` exists with proper configuration

**Option B: Manual JNI Setup**

1. Copy OpenCV native libraries:
   ```bash
   cp -r OpenCV-android-sdk/sdk/native/libs/* app/src/main/jniLibs/
   ```

2. Update `CMakeLists.txt`:
   ```cmake
   set(OpenCV_DIR ${CMAKE_SOURCE_DIR}/src/main/jniLibs/OpenCV/sdk/native/jni)
   find_package(OpenCV REQUIRED)
   include_directories(${OpenCV_INCLUDE_DIRS})
   target_link_libraries(native-lib ${OpenCV_LIBS})
   ```

### Step 4: Configure Build

1. Open project in Android Studio
2. Sync Gradle files: **File → Sync Project with Gradle Files**
3. Verify NDK path in `local.properties`:
   ```properties
   ndk.dir=C\:\\Users\\YourName\\AppData\\Local\\Android\\Sdk\\ndk\\21.4.7075529
   sdk.dir=C\:\\Users\\YourName\\AppData\\Local\\Android\\Sdk
   ```

### Step 5: Build the Project

**Via Android Studio:**
- Click **Build → Make Project** (Ctrl+F9)

**Via Command Line:**
```bash
# Debug build
./gradlew assembleDebug

# Release build
./gradlew assembleRelease
```

### Step 6: Run the App

1. Connect Android device via USB (enable USB debugging)
   - Or start an Android Emulator with camera support
2. Click **Run → Run 'app'** (Shift+F10)
3. Grant camera permission when prompted
4. Choose viewing mode:
   - **Open Camera** → Raw feed
   - **Show Processed Output** → Edge detection

## 🧪 Testing

Run unit tests:
```bash
./gradlew test
```

Run instrumentation tests:
```bash
./gradlew connectedAndroidTest
```

## ⚙️ Configuration

### Adjust Edge Detection Parameters

Edit `ProActivity.java`:

```java
// Modify Canny thresholds
Imgproc.Canny(mRgba, edges, lowThreshold, highThreshold);

// Examples:
// More sensitive (more edges): (50, 150)
// Less sensitive (fewer edges): (100, 250)
// Current default: (80, 200)
```

### Change Camera Resolution

In activity's `onCreate()`:

```java
cameraBridgeViewBase.setMaxFrameSize(1280, 720); // HD
// or
cameraBridgeViewBase.setMaxFrameSize(640, 480);  // VGA
```

## 📂 Project Structure

```
VisionEdge/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/visionedge/detector/
│   │   │   │   ├── MainActivity.java       # Main launcher
│   │   │   │   ├── RawActivity.java       # Raw camera viewer
│   │   │   │   └── ProActivity.java       # Edge detection viewer
│   │   │   ├── res/
│   │   │   │   ├── layout/                # XML layouts
│   │   │   │   ├── values/                # Strings, colors, themes
│   │   │   │   └── drawable/              # Icons and images
│   │   │   ├── jniLibs/                   # Native libraries
│   │   │   └── AndroidManifest.xml
│   │   ├── androidTest/                   # Instrumentation tests
│   │   └── test/                          # Unit tests
│   └── build.gradle                       # App module config
├── sdk/                                   # OpenCV SDK module
├── gradle/                                # Gradle wrapper
├── images/                                # Screenshots
├── build.gradle                           # Root build config
├── settings.gradle                        # Project settings
└── README.md
```

## 🎯 Usage

### Launching the App

1. Open VisionEdge on your device
2. You'll see two options on the home screen

### View Raw Camera Feed

- Tap **"Open Camera"**
- See live camera preview without any processing
- Press back button to return to home

### View Edge Detection

- Tap **"Show Processed Output"**
- Camera feed shows real-time Canny edge detection
- Edges are highlighted in white on black background
- Press back button to return to home

## 🤝 Contributing

Contributions are welcome! Follow these steps:

1. Fork the repository
2. Create feature branch: `git checkout -b feature/AmazingFeature`
3. Commit changes: `git commit -m 'Add AmazingFeature'`
4. Push to branch: `git push origin feature/AmazingFeature`
5. Open a Pull Request

### Development Guidelines

- Follow Java code conventions
- Test on multiple devices/API levels
- Update documentation for new features
- Add unit tests for new functionality

## 📋 Roadmap

- [ ] Add Sobel and Laplacian edge detection
- [ ] Implement threshold adjustment sliders
- [ ] Add photo capture with filters applied
- [ ] Support multiple color spaces (HSV, Lab, YCrCb)
- [ ] Real-time FPS counter overlay
- [ ] Video recording with edge detection
- [ ] Custom filter presets
- [ ] Face detection integration
- [ ] Object tracking capabilities

## 🐛 Troubleshooting

### OpenCV Initialization Failed

**Error:** "OpenCV library not found"

**Solution:**
- Verify OpenCV SDK is correctly placed in `sdk/` folder
- Check `build.gradle` includes `implementation project(path: ':sdk')`
- Clean and rebuild: `Build → Clean Project`

### Camera Permission Denied

**Solution:**
- Manually grant camera permission in device settings
- Uninstall and reinstall the app
- Check `AndroidManifest.xml` has `<uses-permission android:name="android.permission.CAMERA"/>`

### NDK Build Failed

**Solution:**
- Install NDK via SDK Manager
- Verify NDK path in `local.properties`
- Update `gradle.properties` with: `android.useDeprecatedNdk=true`

### App Crashes on Launch

**Solution:**
- Check Logcat for stack trace
- Ensure minimum SDK version is API 26+
- Verify OpenCV native libraries match device architecture (arm64-v8a, armeabi-v7a)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

Developed by **Your Name**  
GitHub: [@yourusername](https://github.com/yourusername)

## 🙏 Acknowledgments

- [OpenCV](https://opencv.org/) - Open Source Computer Vision Library
- [Android Camera2 API](https://developer.android.com/reference/android/hardware/camera2/package-summary) - Modern camera framework
- [Material Components](https://material.io/components) - Material Design UI components
- [OpenGL ES](https://www.khronos.org/opengles/) - Graphics rendering API
- [Android NDK](https://developer.android.com/ndk) - Native development kit

## 📞 Contact & Support

- 📫 **Issues**: [GitHub Issues](https://github.com/yourusername/VisionEdge/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/yourusername/VisionEdge/discussions)
- 📧 **Email**: your.email@example.com

---

<p align="center">
  <b>⭐ Star this repo if you find it useful!</b><br>
  Made with ❤️ using OpenCV and Android
</p>
# VisionEdge
