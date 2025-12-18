# SDL2 Shared Libraries for Android ARM64

This repository provides automated builds of SDL2 and related libraries as shared libraries for Android ARM64 (arm64-v8a) architecture in JNI-compatible directory structure.

## Built Libraries

The GitHub Actions workflow builds the following libraries as shared `.so` files:

- **SDL2** (v2.32.10) - Core SDL2 library
- **SDL2_image** (v2.8.8) - Image loading support (PNG, JPEG, WebP)
- **SDL2_mixer** (v2.8.1) - Audio mixing support (WAV, Vorbis via STB)
- **SDL2_ttf** (v2.24.0) - TrueType font rendering support

## Target Platform

- **Architecture**: ARM64-v8a
- **Android API Level**: 21 (Android 5.0+)
- **NDK Version**: r26c or later (SDL2 2.32.10+ is compatible with NDK r26 and r27+)
- **Library Type**: Shared (.so)

## Output Structure

The build produces libraries organized in a JNI-compatible directory structure:

```
jniLibs/
├── arm64-v8a/
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
3. Download the artifacts:
   - `sdl2-android-arm64-jni.tar.gz` - Complete package with JNI libraries and headers
   - `sdl2-android-arm64-jniLibs` - JNI directory structure

### Extract Libraries

```bash
tar -xzf sdl2-android-arm64-jni.tar.gz
```

This will extract:
- `jniLibs/arm64-v8a/` - Shared library files (*.so)
- `jniLibs/include/` - Header files

### Using in Your Android Project

#### Option 1: Copy to Android Project

Simply copy the `jniLibs` directory to your Android project:

```bash
cp -r jniLibs/ /path/to/your/android/app/src/main/jniLibs/
```

Android will automatically load the `.so` files from the appropriate architecture directory.

#### Option 2: CMakeLists.txt Example

```cmake
# Set the path to SDL2 libraries
set(SDL2_LIB_DIR "${CMAKE_CURRENT_SOURCE_DIR}/path/to/jniLibs/arm64-v8a")
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
LOCAL_SRC_FILES := path/to/jniLibs/arm64-v8a/libSDL2.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_image
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_image
LOCAL_SRC_FILES := path/to/jniLibs/arm64-v8a/libSDL2_image.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_mixer
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_mixer
LOCAL_SRC_FILES := path/to/jniLibs/arm64-v8a/libSDL2_mixer.so
LOCAL_EXPORT_C_INCLUDES := path/to/jniLibs/include
include $(PREBUILT_SHARED_LIBRARY)

# SDL2_ttf
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_ttf
LOCAL_SRC_FILES := path/to/jniLibs/arm64-v8a/libSDL2_ttf.so
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
2. Select "Build SDL2 Shared Libraries for Android ARM64" workflow
3. Click "Run workflow"
4. Select the branch and click "Run workflow"

## Customization

To change library versions or build settings, edit `.github/workflows/build-android-arm64.yml`:

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