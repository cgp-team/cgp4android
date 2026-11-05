# 贡献指南（CargoPostAndroid）

本文面向要在 `cgp-team/cgp4android` 上协作的同学。

## 1. 开发环境

- JDK 21（**必须 21**，AGP 8.13 / Kotlin 2.3 的最低要求）
- Android SDK Platform 36 + Build-Tools 36.0.0
- 本工程 `gradle.properties` 已设 `android.overridePathCheck=true`：仅当工程路径含非 ASCII 字符（如中文用户名）时需要；CI / Linux 上该值保持 UTF-8 即可。

## 2. 本地机密配置（重要）

- `local.properties` **已 gitignore，永远不要提交**，也不要把高德 key / 后端地址 / 签名口令写进源码或 PR。
- 缺失 `local.properties` 时 debug 仍可编译（后端地址空串，App 启动即提示未配置）；release 缺 `buspost.baseUrl` 或签名会**构建失败**（这是故意的护栏）。
- 密钥与签名库向负责人索取；签名材料放在工程外备份目录，不进仓库。

## 3. 分支与提交

- `main` 受保护：**禁止直接 push，必须走 PR**（组织所有者于仓库 `Settings → Branches` 设置）。
- **review 不作强制要求**：PR 提完后，自己或维护者即可合入；但涉及 UI / 接口 / 导航的改动仍建议拉人过一眼。
- 从 `main` 拉特性分支，分支名沿用现有前缀约定：`feat/` `fix/` `docs/` `test/` `ui/` `ledger/` `tools/` …（参考历史提交）。
- 提交信息沿用仓库现有风格，用前缀点明类型，例如：
  - `fix(...)` 修 bug
  - `feat(...)` 新能力
  - `docs(...)` 文档
  - `ledger(...)` 台账 / 验收记录
  - `ui(...)` 界面

## 4. 设计令牌与配色（红线）

- 颜色 / 字号的唯一真源是 `core/ui/BusTokens.kt` + `BusTheme.kt`（4 主题 + 深色）。**页面 / 组件只引用令牌，不得手写色值。**
- 深色模式：主题色不能直接当文字用；弱化靠字号 / 字重 / 位置，不用透明度表达层级。
- 稻谷金等分底使用（浅底深金 `#8A6A1F`、深底亮金 `#E8C271`），不可压在主色上做图形。
- 改 UI 前先确认新增色值满足 WCAG AA 对比度（本仓以令牌为准，不手写）。

## 5. 依赖红线

- **高德只用 `com.amap.api:navi-3dmap`**，它已内置 location / search 类。**不得再引 `com.amap.api:location`**（duplicate class，构建假通过、`assemble` 阶段才暴露）。
- 版本上限是被 AAR 元数据卡住的合法值，不是「越大越好」：Compose BOM 2026.06.01、AGP 8.13.2、navigation-compose 2.9.4、lifecycle 2.10.0。升级任何依赖后必须让 `./gradlew :app:checkDebugAarMetadata` 放行。
- 新增依赖后**必须联网构建一次**（`--offline` 会漏下 AAR，`assemble` 阶段才暴露）。

## 6. 演示数据

- 发布包 `DEMO_ALLOWED=false` 硬编码；debug 默认也为 false。后端不通就是错误条，不会回落 DemoData。**不要为了「看起来能跑」打开演示数据。**

## 7. 不要提交这些

`local.properties`、所有 `*.jks` / `*.keystore` / `keystore.properties`、整个 `build/`、`tmp/`（本机 AI 工作备份）、`*.log`、`.apk` / `.aab`。这些已在 `.gitignore` 覆盖。

## 8. 真机验证

本工程 CI 无模拟器 / 真机，只能验证到**编译级**。涉及界面渲染、相机、地图、GPS、推送的改动，必须在真机（Android 16 / SDK 36，arm64）走查后再合入。

## 9. PR 流程

1. fork / 拉分支 → 本地改 → 自测 `assembleDebug` + `testDebugUnitTest`。
2. 开 PR 到 `main`，描述「改了什么 / 为什么 / 真机走查结论」。
3. CI 绿 + 描述清楚后，自己或维护者合入。
