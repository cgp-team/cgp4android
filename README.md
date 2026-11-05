# 乡邮智联 · Android 客户端（CargoPostAndroid）

> 客货邮一体化平台 Android 客户端：Jetpack Compose + 高德导航，司机端 / 村民端双业务域，单 APK。

- 应用包名：`com.shanxiang.buspost`
- 所属组织：`cgp-team`（本仓库 `cgp-team/cgp4android`；旧个人号 `ycg-coder/cargo-post-android` 已归档）
- 技术栈：Kotlin 2.3 · Jetpack Compose（BOM 2026.06.01）· AGP 8.13.2 · 高德 navi-3dmap
- 最低 / 目标 / 编译 SDK：minSdk 26 / targetSdk 36 / compileSdk 36，Java 17

## 快速开始

**前置条件**

- JDK 21（构建用；AGP 8.13 / Kotlin 2.3 的最低要求，本机用 Temurin / Oracle JDK 21）
- Android SDK Platform 36 + Build-Tools 36.0.0（首次构建 Gradle 会按需解析）
- Android Studio / IntelliJ IDEA（可选，命令行亦可）

**1. 克隆**

```bash
git clone https://github.com/cgp-team/cgp4android.git
cd cgp4android
```

**2. 本地配置 `local.properties`**（该文件已 gitignore，**切勿提交**，密钥向负责人索取）

```properties
# 后端地址，只写到前缀为止（Repo 内路径已带 /app-api 前缀，别再加）
buspost.baseUrl=https://your-domain/api
# 高德「Android 平台」key（绑包名 + 签名 SHA1），注入 manifest
amap.key=你的Android平台key
# 高德「Web 服务」key（天气 REST），不注入 manifest
amap.webKey=你的Web服务key
```

> 配置优先级（详见 `app/build.gradle.kts`）：`-Pbuspost.xxx` > 环境变量 `BUSPOST_XXX` > `local.properties`。
> debug 构建**不需要**这些项（留空时 App 启动会提示「未配置后端地址」）；release 构建**必须**提供 HTTPS 的 `buspost.baseUrl`，否则构建失败。

**3. 构建**

```bash
./gradlew :app:assembleDebug      # 调试包（无需密钥，可直接装真机）
./gradlew :app:assembleRelease    # 正式包（需签名 + HTTPS 后端地址）
```

产物：`app/build/outputs/apk/debug/app-debug.apk`（或 `release/`）。

## 发布构建

- 签名材料**不进源码工程**（即便 gitignore 也容易误加）。默认从工程同级目录 `../buspost-signing-backup/keystore.properties` 读取；CI 用 `-Pbuspost.signingProps=` 或环境变量 `BUSPOST_SIGNING_PROPS` 注入。
- release 包禁止演示数据（`DEMO_ALLOWED=false` 硬编码），后端不通就是错误条，不会回落假数据。
- 仅发 64 位 ARM（`arm64-v8a`）：32 位老机器将装不了正式版（详见 `app/build.gradle.kts` 注释）。
- 版本号：`versionCode` 走 CI 的 `GITHUB_RUN_NUMBER`，本地恒为 1；App 内检查更新只比 `versionCode`。

## 测试

```bash
./gradlew :app:testDebugUnitTest
```

单元测试跑在 JVM 上；接口 / 地图 / 真机渲染本工程 CI 未覆盖（无模拟器 / 真机），需真机走查。

## 目录结构（要点）

- `app/` —— 应用模块（`build.gradle.kts` 是唯一构建真源）
- `core/ui/` —— 设计令牌 `BusTokens.kt` / `BusTheme.kt`（颜色、字号、主题的唯一真源）
- `ui/user/` `ui/driver/` `driver/` —— 用户域 / 司机域（两域隔离，见 `docs/`）
- `utils/` —— 接口层、天气、二维码、地图封装
- `tools/` —— 取证 / 校验脚本（如 `release_minify_evidence.sh`、`check_startup_probes.sh`）
- `docs/` —— 架构与审计文档（见 `docs/android/README.md` 指路牌，不要在多处复制同一事实）

## 第三方与许可

随 APK 分发的构件与许可证见仓库根 `THIRD_PARTY_NOTICES.md`（指令 v2.0 §100 要求）。

## 贡献

见 [CONTRIBUTING.md](./CONTRIBUTING.md)。
