# DiPlay — 社区分支（FangJInGuang fork）

[English](README.md) · [简体中文](README.zh-CN.md) · [繁體中文](README.zh-TW.md)

**适用于比亚迪安卓车机的 CarPlay 投屏。本分支在官方 [DiPlay](https://github.com/shihabal3amri/DiPlay)（v0.2.21）基础上，仅增加一项实验性功能。**

> **本仓库为社区分支，非官方发布版。** 上游：[shihabal3amri/DiPlay](https://github.com/shihabal3amri/DiPlay) · 功能合并请求：[#1081](https://github.com/shihabal3amri/DiPlay/pull/1081)

## 新增功能：CarPlay 画中画支持（实验性）

**设置 → 高级 → 三方启动器支持 → CarPlay 画中画支持（实验性）** —— 默认关闭。

三方桌面（如嘟嘟桌面）的画中画卡片只能显示 DiPlay 主页面所在的任务。自上游 0.2.12 起 CarPlay 画面运行在独立任务中（`taskAffinity="${applicationId}.carplay"`），打开 CarPlay 后卡片即失去画面。开启本开关后，CarPlay 页面与主页面保持同一任务，卡片可在预连接 → 正在连接 → CarPlay 全程持续显示。

- 实现方式：新增第二个 Activity 类（`CarPlayHostActivityMainTask`，无 taskAffinity）+ 统一启动路由（覆盖全部启动入口）+ onCreate 守卫（兜底 USB 直连、外部显式指定组件等绕过路由的入口）。`taskAffinity` 为编译期静态属性，运行时无法修改；activity-alias 也无法表达不同亲和性（AOSP `PackageParser.parseActivityAlias` 强制继承目标任务的亲和性），因此必须使用真实的第二个类。
- 下次打开 CarPlay 时生效。
- **需配合官方已有的「画中画与分屏自适应」（adapt_pip_resolution）开关**，以获得自适应画布、无黑边。

### 实车验证

- **比亚迪驱逐舰 05（13.1 系统 / DiLink 3.0）+ 嘟嘟桌面（比亚迪专享版）验证通过**：开启开关后，预连接、正在连接、CarPlay 画面均可在卡片内显示。
- 预期现象（非回归）：点「立即连接」会短暂跳出卡片（按返回键重新嵌入）；通过嘟嘟打开全屏、拖动卡片大小、全屏 ⇄ 画中画切换会触发 CarPlay 重连（属上游既有画布/显示变更机制，与本改动无关）。
- 实验性标注：仅在该车型 + 该桌面验证，其他设备兼容性持续跟进。

上车实拍（嘟嘟桌面）：

<img src="screenshots/pip-carplay-dark.png" width="30%" alt="嘟嘟桌面（深色）" />
<img src="screenshots/pip-carplay-light.png" width="30%" alt="嘟嘟桌面（浅色）" />
<img src="screenshots/IMG_0396.jpeg" width="30%" alt="上车实拍" />

## 其余部分

其余代码、设置、文档与应用行为与上游 [v0.2.21](https://github.com/shihabal3amri/DiPlay/releases/tag/v0.2.21) 完全一致。

- 安装与连接：[docs/INSTALL.md](docs/INSTALL.md)
- 源码构建：[docs/BUILD.md](docs/BUILD.md)
- 兼容性与排障：[docs/COMPATIBILITY.md](docs/COMPATIBILITY.md)
- 诊断与隐私：[docs/PRIVACY.md](docs/PRIVACY.md)

> **本仓库不含任何配件（MFI）身份资产**（遵循上游构建规范）；源码/CI 构建默认不含运行时身份资产，本地发布打包方式见上游构建说明。
