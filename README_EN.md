# Android Xposed Module Project Generator

A project generator based on [EasyXposed](https://github.com/zhongqingsong/EasyXposed) template, designed specifically for **Termux aarch64 environment** with automatic build dependency configuration.

## Features

✅ **One-Click Generation** - Interactive Q&A, automatically creates complete project structure  
✅ **Termux Optimized** - Auto-configures `aapt2` path and Gradle mirror sources  
✅ **Zero Pitfalls** - Pre-configured with all aarch64 build requirements  
✅ **Great Compatibility** - Supports both LSPosed and standard Xposed frameworks  

## Prerequisites

### 1. Install Termux Build Toolchain

```bash
# Basic packages
pkg install -y git openjdk-17 gradle

# Android SDK
mkdir -p ~/android/sdk && cd ~/android/sdk
wget https://mirrors.cloud.tencent.com/AndroidSDK/commandlinetools-linux-11076708_latest.zip
unzip commandlinetools-*.zip
mkdir -p cmdline-tools/latest
mv cmdline-tools/{bin,lib,NOTICE.txt,source.properties} cmdline-tools/latest/

# Configure environment variables (add to ~/.bashrc)
export ANDROID_HOME=$HOME/android/sdk
export PATH=$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH

# Install SDK components
yes | sdkmanager --licenses
sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# Download aarch64 tools (Critical!)
wget https://github.com/lzhiyong/android-sdk-tools/releases/download/34.0.3/android-sdk-tools-static-aarch64.zip
unzip android-sdk-tools-static-aarch64.zip
cp build-tools/* $ANDROID_HOME/build-tools/34.0.0/
cp platform-tools/* $ANDROID_HOME/platform-tools/
chmod +x $ANDROID_HOME/build-tools/34.0.0/aapt2
```

### 2. Clone EasyXposed Template

```bash
git clone https://github.com/zhongqingsong/EasyXposed.git ~/EasyXposed
```

## Installation

```bash
# Download script
curl -o ~/bin/android-init https://raw.githubusercontent.com/gongxi888/android-xposed-init/main/android-init
chmod +x ~/bin/android-init

# Add to PATH (add to ~/.bashrc)
export PATH=~/bin:$PATH
```

## Usage

```bash
android-init
```

Follow the prompts:
- **Project Name**: English name, e.g., `MyHookModule`
- **Package Name**: e.g., `com.example.myhook`
- **Module Description**: Description shown in LSPosed
- **Author Name**: Your name

After generation:
```bash
cd ~/YourProjectName
./gradlew build
```

## Automatic Configurations

The generator automatically handles:

### 1. Gradle Mirror (Speeds up downloads)
```properties
# gradle/wrapper/gradle-wrapper.properties
distributionUrl=https://mirrors.cloud.tencent.com/gradle/gradle-8.0-bin.zip
```

### 2. aapt2 Path Override (aarch64 compatibility)
```properties
# gradle.properties
android.aapt2FromMavenOverride=/data/data/com.termux/files/home/android/sdk/build-tools/34.0.0/aapt2
```

### 3. Dependency Mirrors (Aliyun)
```gradle
// settings.gradle
repositories {
    maven { url 'https://maven.aliyun.com/repository/google' }
    maven { url 'https://maven.aliyun.com/repository/public' }
    maven { url 'https://api.xposed.info/' }
    google()
    mavenCentral()
}
```

### 4. SDK Path
```properties
# local.properties
sdk.dir=/data/data/com.termux/files/home/android/sdk
```

## Development Entry Points

Main files in generated project:

- **Hook Logic**: `app/src/main/java/your/package/EasyHooker.java`
- **Utilities**: `Tool.java` (logging, stack traces)
- **Config**: `app/src/main/assets/xposed_init`

## Troubleshooting

### Build fails: `Unsupported class file major version`
**Cause**: Java version mismatch  
**Solution**: Switch to Java 17
```bash
export PATH=/data/data/com.termux/files/usr/lib/jvm/java-17-openjdk/bin:$PATH
export JAVA_HOME=/data/data/com.termux/files/usr/lib/jvm/java-17-openjdk
```

### `aapt2: Syntax error: Unterminated quoted string`
**Cause**: aapt2 override not configured  
**Solution**: Check if `gradle.properties` contains `android.aapt2FromMavenOverride`

## Credits

- [EasyXposed](https://github.com/zhongqingsong/EasyXposed) - Original template
- [lzhiyong/android-sdk-tools](https://github.com/lzhiyong/android-sdk-tools) - aarch64 tools

## License

MIT License
