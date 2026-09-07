# 拾光课表

拾光课表是一个面向学生的课表与每日任务应用。它以离线优先的 Web/PWA 为核心，并通过 Capacitor 封装为 Android App；课程、学期设置和待办数据默认保存在用户自己的设备上。

## 主要功能

- 查看一学期内各周的 14 节课表，并显示日期、星期和节次时间。
- 添加、编辑和删除课程，支持单节或连续多节课程，以及按周重复。
- 查看课程地点、时间和学分等详情。
- 管理今日待办，支持日期、时间、完成勾选和任务开始前 15 分钟的浏览器通知。
- 响应式界面、离线缓存和 Android APK 封装。

## 技术栈

- 原生 HTML、CSS、JavaScript Web 前端
- PWA：Web App Manifest、Service Worker、浏览器 `localStorage`
- Capacitor 7.4.3
- Android Gradle Plugin 8.7.2、Gradle 8.11.1、JDK 17
- Node.js 20 或更高版本、pnpm（推荐）

## 项目结构

```text
.
├─ outputs/                 # Web/PWA 源文件（Capacitor 的 webDir）
│  ├─ index.html
│  ├─ app.js
│  ├─ styles.css
│  ├─ manifest.json
│  └─ sw.js
├─ android/                 # Capacitor Android 工程
├─ capacitor.config.ts      # Capacitor 配置
├─ package.json             # npm/pnpm 脚本和依赖
├─ pnpm-lock.yaml           # pnpm 锁文件
└─ .gitignore
```

`android/app/src/main/assets/public/` 是 Capacitor 生成的 Web 资源目录，不是 Web 源码目录，已加入忽略规则。Android 构建前会由 Capacitor 同步，且工程中的 `syncWebAssets` 任务会从 `outputs/` 更新它。因此从 GitHub 全新克隆后不需要提交该目录。

## Windows 开发环境要求

- Windows 10/11
- Node.js 20+
- pnpm 9+（推荐；也可以使用 npm，但执行 Android 构建前应运行 `npx cap sync android` 重新生成 Capacitor 文件）
- Android Studio，包含 Android SDK Platform 35、Build Tools 和 Android SDK Command-line Tools
- JDK 17。项目已按 Java 17 编译；可使用 Android Studio 自带 JDK 或其他 JDK 17 安装
- Git

请在 Android Studio 的 SDK Manager 中安装并接受 Android SDK 许可。项目保持 `compileSdkVersion = 35` 和 `targetSdkVersion = 35`。

## 安装依赖

在项目根目录执行：

```powershell
pnpm install
```

如使用 npm：

```powershell
npm install
```

依赖安装目录 `node_modules/` 不应提交到 GitHub。

## 运行 Web/PWA

可以直接部署 `outputs/` 到静态服务器。也可以使用 Python 启动本地静态服务器：

```powershell
cd outputs
python -m http.server 8080
```

然后打开 <http://localhost:8080>。浏览器会将课表和待办保存在当前设备的 `localStorage` 中。

## 构建 Android APK

首次克隆或依赖更新后，先同步 Capacitor Android 工程：

```powershell
pnpm exec cap sync android
```

使用指定 Windows 环境变量构建 Debug APK：

```powershell
cd android
$env:ANDROID_SDK_ROOT = "C:\Users\<你的用户名>\AppData\Local\Android\Sdk"
$env:ANDROID_HOME = $env:ANDROID_SDK_ROOT
$env:ANDROID_USER_HOME = "$env:USERPROFILE\.android"
$env:JAVA_HOME = "C:\Program Files\Android\Android Studio\jbr"
.\gradlew.bat assembleDebug
```

也可以从项目根目录执行：

```powershell
pnpm run android:build
```

APK 输出位置：

```text
android\app\build\outputs\apk\debug\app-debug.apk
```

构建目录和 APK 是本地生成物，已被 `.gitignore` 排除。发布到应用商店前需要在 Android Studio 中配置正式签名和 Release 构建。

## iOS 后续开发

当前仓库暂未包含 iOS 工程。后续可在 macOS、Xcode 和 CocoaPods 环境中执行：

```bash
npx cap add ios
npx cap sync ios
```

然后使用 Xcode 配置 Bundle Identifier、签名和通知权限。现有 Web 前端可以继续复用。

## 隐私与账号安全

- 本项目没有登录系统，也不需要学号、密码、Cookie、Token、API Key 或服务器账号。
- 课程和任务默认只保存在用户本地浏览器或 App WebView 的 `localStorage` 中。
- 仓库不应提交 `.env`、`local.properties`、签名密钥、Google Services 配置、Token 或其他个人配置文件。
- 通知权限由浏览器或系统控制；项目不会把课程和任务上传到远程服务。

## 已知问题

- 当前数据是本地单设备数据，没有账号登录、云同步或多设备同步。
- Android 调试构建依赖本机 Android SDK、JDK 和网络可用的 Maven 依赖缓存。
- Capacitor 的 `android/` 生成配置会随依赖管理器变化；使用 npm 重新安装后请先运行 `npx cap sync android`。
- iOS 原生工程和 App Store 发布配置尚未完成。
- 依赖审计可能会报告 Capacitor CLI 间接依赖的开发工具漏洞；发布前应按依赖升级计划重新审计。

## 项目用途声明

本项目仅用于学习和个人研究。请遵守所在学校的课表、网络、通知和数据使用规定，不要将本项目用于未经授权的数据抓取、账号登录或其他违反学校规章制度的用途。

## 许可证

本项目使用 MIT License，详见 [LICENSE](LICENSE)。
