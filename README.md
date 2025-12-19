# SDL2 Shared Libraries for Android

This repository provides automated builds of SDL2 and related libraries as shared libraries for Android (arm64-v8a and x86_64) architectures in JNI-compatible directory structure.

## Built Libraries

The GitHub Actions workflow builds the following libraries as shared `.so` files:

- **SDL2** (v2.32.10) - Core SDL2 library
- **SDL2_image** (v2.8.8) - Image loading support (PNG, JPEG, WebP)
- **SDL2_mixer** (v2.8.1) - Audio mixing support (WAV, Vorbis via STB)
- **SDL2_ttf** (v2.24.0) - TrueType font rendering support

## Target Platform

- **Architectures**: ARM64-v8a, x86_64
- **Android API Level**: 21 (Android 5.0+)
- **NDK Version**: r26c or later (SDL2 2.32.10+ is compatible with NDK r26 and r27+)
- **Library Type**: Shared (.so)

## Output Structure

The build produces libraries organized in a JNI-compatible directory structure for each architecture:

```
jniLibs/
├── arm64-v8a/  (or x86_64/)
│   ├── libSDL2.so
│   ├── libSDL2_image.so
│   ├── libSDL2_mixer.so
│   └── libSDL2_ttf.so
└── include/
    ├── SDL2/
    └── ...
```

## Usage

### Download Pre-built Libraries

1. Go to the [Actions](../../actions) tab
2. Select the latest successful workflow run
3. Download the artifacts for your target architecture:
   - **ARM64**: `sdl2-android-arm64-v8a-jni.tar.gz` - Complete package with JNI libraries and headers
   - **x86_64**: `sdl2-android-x86_64-jni.tar.gz` - Complete package with JNI libraries and headers
   - Or download the `sdl2-android-{arch}-jniLibs` directory artifacts

### Extract Libraries

```bash
# For ARM64
tar -xzf sdl2-android-arm64-v8a-jni.tar.gz

# For x86_64
tar -xzf sdl2-android-x86_64-jni.tar.gz
```

This will extract:
- `jniLibs/arm64-v8a/` (or `jniLibs/x86_64/`) - Shared library files (*.so)
- `jniLibs/include/` - Header files

### Using Multiple Architectures

To support multiple architectures in your Android project, download and extract both architecture packages, then merge them:

```bash
# Extract ARM64
tar -xzf sdl2-android-arm64-v8a-jni.tar.gz
mv jniLibs jniLibs-temp

# Extract x86_64
tar -xzf sdl2-android-x86_64-jni.tar.gz

# Merge architectures (copy ARM64 into the same structure)
cp -r jniLibs-temp/arm64-v8a jniLibs/
rm -rf jniLibs-temp

# Now jniLibs contains both architectures:
# jniLibs/
# ├── arm64-v8a/
# │   └── *.so files
# ├── x86_64/
# │   └── *.so files
# └── include/
```

### Using in Your Android Project

#### Option 1: Copy to Android Project

Simply copy the `jniLibs` directory to your Android project:

```bash
cp -r jniLibs/ /path/to/your/android/app/src/main/jniLibs/
```

Android will automatically load the `.so` files from the appropriate architecture directory.

#### Option 2: CMakeLists.txt Example

```cmake
# Set the path to SDL2 libraries (adjust path for your architecture)
set(SDL2_LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}/path/to/jniLibs/${ANDROID_ABI}")
set(SDL2_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/path/to/jniLibs/include")

# Add include directories
include_directories(${SDL2_INCLUDE_DIR})
include_directories(${SDL2_INCLUDE_DIR}/SDL2)

# Link SDL2 shared libraries
target_link_libraries(your_target
    ${SDL2_LIB_DIR}/libSDL2.so
    ${SDL2_LIB_DIR}/libSDL2_image.so
    ${SDL2_LIB_DIR}/libSDL2_mixer.so
    ${SDL2_LIB_DIR}/libSDL2_ttf.so
    # Android system libraries required by SDL2
    android
    log
    GLESv1_CM
    GLESv2
    OpenSLES
)
```

#### Option 3: Android.mk Example

```makefile
LOCAL_PATH := $(call my-dir)

# SDL2
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2
LOCAL_SRC_FILES := path/to/jniLibs/$(TARGET_ARCH_ABI)/libSDL2.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_image
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_image
LOCAL_SRC_FILES := path/to/jniLibs/$(TARGET_ARCH_ABI)/libSDL2_image.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_mixer
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_mixer
LOCAL_SRC_FILES := path/to/jniLibs/$(TARGET_ARCH_ABI)/libSDL2_mixer.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_ttf
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_ttf
LOCAL_SRC_FILES := path/to/jniLibs/$(TARGET_ARCH_ABI)/libSDL2_ttf.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# Your application
include $(CLEAR_VARS)
LOCAL_MODULE := your_app
LOCAL_SRC_FILES := your_source.cpp
LOCAL_SHARED_LIBRARIES := SDL2 SDL2_image SDL2_mixer SDL2_ttf
LOCAL_LDLIBS := -llog -landroid -lGLESv1_CM -lGLESv2 -lOpenSLES
include $(BUILD_SHARED_LIBRARY)
```

## Building Manually

To trigger a build manually:

1. Go to the [Actions](../../actions) tab
2. Select "Build SDL2 Shared Libraries for Android" workflow
3. Click "Run workflow"
4. Select the branch and click "Run workflow"

The workflow will automatically build for both arm64-v8a and x86_64 architectures in parallel.

## Customization

To change library versions, architectures, or build settings, edit `.github/workflows/build-android-arm64.yml`:

- `matrix.abi` - Target architectures (currently: arm64-v8a, x86_64)
- `SDL2_VERSION` - SDL2 version
- `SDL2_IMAGE_VERSION` - SDL2_image version
- `SDL2_MIXER_VERSION` - SDL2_mixer version
- `SDL2_TTF_VERSION` - SDL2_ttf version
- `ANDROID_API_LEVEL` - Minimum Android API level
- `ANDROID_NDK_VERSION` - NDK version to use

## License

The build scripts in this repository are provided as-is. The SDL2 libraries themselves are licensed under the zlib license. Please refer to the individual library repositories for their respective licenses:

- [SDL2](https://github.com/libsdl-org/SDL)
- [SDL2_image](https://github.com/libsdl-org/SDL_image)
- [SDL2_mixer](https://github.com/libsdl-org/SDL_mixer)
- [SDL2_ttf](https://github.com/libsdl-org/SDL_ttf)