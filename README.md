# Android Xposed 模块项目生成器

基于 [EasyXposed](https://github.com/zhongqingsong/EasyXposed) 模板的项目生成器，专为 **Termux aarch64 环境**设计，自动配置所有编译依赖。

## 特性

✅ **一键生成** - 交互式问答，自动创建完整项目结构  
✅ **Termux 优化** - 自动配置 `aapt2` 路径和 Gradle 镜像源  
✅ **零坑环境** - 预配置所有 aarch64 编译所需设置  
✅ **兼容性好** - 支持 LSPosed 和标准 Xposed 框架  

## 前置要求

### 1. 安装 Termux 编译工具链

```bash
# 基础包
pkg install -y git openjdk-17 gradle

# Android SDK
mkdir -p ~/android/sdk && cd ~/android/sdk
wget https://mirrors.cloud.tencent.com/AndroidSDK/commandlinetools-linux-11076708_latest.zip
unzip commandlinetools-*.zip
mkdir -p cmdline-tools/latest
mv cmdline-tools/{bin,lib,NOTICE.txt,source.properties} cmdline-tools/latest/

# 配置环境变量（写入 ~/.bashrc）
export ANDROID_HOME=$HOME/android/sdk
export PATH=$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools:$PATH

# 安装 SDK 组件
yes | sdkmanager --licenses
sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"

# 下载 aarch64 工具（关键！）
wget https://github.com/lzhiyong/android-sdk-tools/releases/download/34.0.3/android-sdk-tools-static-aarch64.zip
unzip android-sdk-tools-static-aarch64.zip
cp build-tools/* $ANDROID_HOME/build-tools/34.0.0/
cp platform-tools/* $ANDROID_HOME/platform-tools/
chmod +x $ANDROID_HOME/build-tools/34.0.0/aapt2
```

### 2. 克隆 EasyXposed 模板

```bash
git clone https://github.com/zhongqingsong/EasyXposed.git ~/EasyXposed
```

## 安装生成器

```bash
# 下载脚本
curl -o ~/bin/android-init https://raw.githubusercontent.com/gongxi888/android-xposed-init/main/android-init
chmod +x ~/bin/android-init

# 添加到 PATH（写入 ~/.bashrc）
export PATH=~/bin:$PATH
```

## 使用方法

```bash
android-init
```

按提示输入：
- **项目名**：英文名称，如 `MyHookModule`
- **包名**：如 `com.example.myhook`
- **模块描述**：显示在 LSPosed 中的说明
- **作者名**：你的名字

生成完成后：
```bash
cd ~/你的项目名
./gradlew build
```

## 自动配置项

生成器会自动处理以下配置：

### 1. Gradle 镜像源（解决下载慢）
```properties
# gradle/wrapper/gradle-wrapper.properties
distributionUrl=https://mirrors.cloud.tencent.com/gradle/gradle-8.0-bin.zip
```

### 2. aapt2 路径覆盖（解决 aarch64 兼容）
```properties
# gradle.properties
android.aapt2FromMavenOverride=/data/data/com.termux/files/home/android/sdk/build-tools/34.0.0/aapt2
```

### 3. 依赖镜像源（阿里云）
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

### 4. SDK 路径
```properties
# local.properties
sdk.dir=/data/data/com.termux/files/home/android/sdk
```

## 开发入口

生成的项目主要文件：

- **Hook 逻辑**：`app/src/main/java/你的包名/EasyHooker.java`
- **工具类**：`Tool.java`（日志、堆栈跟踪）
- **配置**：`app/src/main/assets/xposed_init`

## 故障排查

### 编译失败：`Unsupported class file major version`
**原因**：Java 版本不匹配  
**解决**：切换到 Java 17
```bash
export PATH=/data/data/com.termux/files/usr/lib/jvm/java-17-openjdk/bin:$PATH
export JAVA_HOME=/data/data/com.termux/files/usr/lib/jvm/java-17-openjdk
```

### `aapt2: Syntax error: Unterminated quoted string`
**原因**：未配置 aapt2 覆盖  
**解决**：检查 `gradle.properties` 是否包含 `android.aapt2FromMavenOverride`

## 致谢

- [EasyXposed](https://github.com/zhongqingsong/EasyXposed) - 原始模板
- [lzhiyong/android-sdk-tools](https://github.com/lzhiyong/android-sdk-tools) - aarch64 工具

## 许可证

MIT License
