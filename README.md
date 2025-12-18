# SDL2 Static Libraries for Android ARM64

This repository provides automated builds of SDL2 and related libraries as static libraries for Android ARM64 (arm64-v8a) architecture.

## Built Libraries

The GitHub Actions workflow builds the following libraries as static `.a` files:

- **SDL2** (v2.30.0) - Core SDL2 library
- **SDL2_image** (v2.8.2) - Image loading support (PNG, JPEG, WebP)
- **SDL2_mixer** (v2.8.0) - Audio mixing support (Vorbis, Opus, FLAC, MP3)
- **SDL2_ttf** (v2.22.0) - TrueType font rendering support

## Target Platform

- **Architecture**: ARM64-v8a
- **Android API Level**: 21 (Android 5.0+)
- **NDK Version**: r26c
- **Library Type**: Static (.a)

## Usage

### Download Pre-built Libraries

1. Go to the [Actions](../../actions) tab
2. Select the latest successful workflow run
3. Download the artifacts:
   - `sdl2-android-arm64-static.tar.gz` - Complete package with libraries and headers
   - `sdl2-android-arm64-libs` - Separate lib/ and include/ directories

### Extract Libraries

```bash
tar -xzf sdl2-android-arm64-static.tar.gz
```

This will extract:
- `lib/` - Static library files (*.a)
- `include/` - Header files

### Using in Your Android Project

#### CMakeLists.txt Example

```cmake
# Set the path to SDL2 libraries
set(SDL2_DIR "${CMAKE_CURRENT_SOURCE_DIR}/path/to/sdl2/lib")
set(SDL2_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/path/to/sdl2/include")

# Add include directories
include_directories(${SDL2_INCLUDE_DIR})
include_directories(${SDL2_INCLUDE_DIR}/SDL2)

# Link SDL2 libraries
target_link_libraries(your_target
    ${SDL2_DIR}/libSDL2.a
    ${SDL2_DIR}/libSDL2_image.a
    ${SDL2_DIR}/libSDL2_mixer.a
    ${SDL2_DIR}/libSDL2_ttf.a
    # Android system libraries required by SDL2
    android
    log
    GLESv1_CM
    GLESv2
    OpenSLES
)
```

#### Android.mk Example

```makefile
LOCAL_PATH := $(call my-dir)

# SDL2
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2
LOCAL_SRC_FILES := path/to/sdl2/lib/libSDL2.a
LOCAL_EXPORT_C_INCLUDES := path/to/sdl2/include
include $(PREBUILT_STATIC_LIBRARY)

# SDL2_image
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_image
LOCAL_SRC_FILES := path/to/sdl2/lib/libSDL2_image.a
LOCAL_EXPORT_C_INCLUDES := path/to/sdl2/include
include $(PREBUILT_STATIC_LIBRARY)

# SDL2_mixer
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_mixer
LOCAL_SRC_FILES := path/to/sdl2/lib/libSDL2_mixer.a
LOCAL_EXPORT_C_INCLUDES := path/to/sdl2/include
include $(PREBUILT_STATIC_LIBRARY)

# SDL2_ttf
include $(CLEAR_VARS)
LOCAL_MODULE := SDL2_ttf
LOCAL_SRC_FILES := path/to/sdl2/lib/libSDL2_ttf.a
LOCAL_EXPORT_C_INCLUDES := path/to/sdl2/include
include $(PREBUILT_STATIC_LIBRARY)

# Your application
include $(CLEAR_VARS)
LOCAL_MODULE := your_app
LOCAL_SRC_FILES := your_source.cpp
LOCAL_STATIC_LIBRARIES := SDL2 SDL2_image SDL2_mixer SDL2_ttf
LOCAL_LDLIBS := -llog -landroid -lGLESv1_CM -lGLESv2 -lOpenSLES
include $(BUILD_SHARED_LIBRARY)
```

## Building Manually

To trigger a build manually:

1. Go to the [Actions](../../actions) tab
2. Select "Build SDL2 Static Libraries for Android ARM64" workflow
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