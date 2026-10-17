# 一加 Ace 3 Pro (PJX110) GKI 内核构建

参考 [ShirkNeko/Action_OnePlus_MKSU_SUSFS](https://github.com/ShirkNeko/Action_OnePlus_MKSU_SUSFS) 的 GKI 构建方式，
针对 **一加 Ace 3 Pro（型号 PJX110 / sm8650）** 的当前系统 **ColorOS 16 / PJX110_16.0.5.1001（Android 16）**
源码重新适配，自动编译带 **SukiSU Ultra + SUSFS** 的内核，并打包成 AnyKernel3 刷机包。

## 构建目标

| 项目 | 值 |
| --- | --- |
| 机型 | 一加 Ace 3 Pro / PJX110 |
| 平台 | sm8650（骁龙 8 Gen 3）/ 处理器代号 pineapple |
| 系统版本 | PJX110_16.0.5.1001（ColorOS 16 / Android 16） |
| 内核版本 | 6.1.141（源码 Makefile: 6.1.141） |
| 源码分支 | `OnePlusOSS/kernel_manifest` → `oneplus/sm8650` |
| 源码 manifest | `oneplus_ace3_pro_b.xml`（对应 `oneplus/sm8650_b_16.0.0_ace_3_pro`） |
| Root 方案 | SukiSU Ultra（内置模式） |
| 隐藏方案 | SUSFS（分支 `gki-android14-6.1`） |
| 打包 | AnyKernel3 |

> **关于 GKI 安卓版本**：6.1 内核的 GKI 基线是 `android14-6.1`（Android 15 的 GKI 是 6.6，Android 16 的 GKI 是 6.12）。
> 因此 SUSFS 补丁必须使用 `gki-android14-6.1`，这与 ColorOS 的版本号（16）无关。
> 目前 SUSFS 仅支持 `android14/6.1`、`android15/6.6`、`android16/6.12` 三组组合。

## 使用方法

1. **Fork / 新建仓库**：在 GitHub 上新建一个仓库，把本目录下的 `.github/workflows/build-ace3pro.yml` 放到相同路径。
2. **启用 Actions**：进入仓库的 Actions 页面，允许工作流运行。
3. **手动触发**：选择 `Build OnePlus Ace 3 Pro (PJX110) Kernel` → `Run workflow`。
4. **选择参数**（一般保持默认即可）：

   | 参数 | 默认 | 说明 |
   | --- | --- | --- |
   | BUILD_METHOD | gki | `gki` 通用内核 / `perf` 性能内核 |
   | SUSFS | true | 启用 SUSFS 隐藏 |
   | SUSFS_CI | true | SUSFS 模块用 CI 构建（关闭则用 Release 版） |
   | KPM | true | 启用 KPM |
   | LZ4KD | true | 启用 LZ4KD（ZRAM 增强算法） |
   | VFS | true | 启用手动钩子（仅影响产物命名） |

5. 构建完成后在 Actions 运行的 **Artifacts** 中下载 `AnyKernel3_PJX110_6.1.141_*`。

## 刷入

1. **务必先备份**当前 `boot.img`（可用 `dd` 或内核管理器导出），以便出问题时恢复。
2. 把 AnyKernel3 zip 通过自定义 Recovery（如 TWRP）或内核管理器刷入 **boot** 分区。
3. 安装与本次构建匹配的 **SukiSU 管理器 APK**（SukiSU-Ultra 官方发布页获取；SUSFS 模块已随包放入，刷入后自动安装）。

> CMD 示例（adb sideload，需在 Recovery 中）：
> `adb sideload AnyKernel3_PJX110_6.1.141_xxxx.zip`

## 注意

- 刷内核属于高风险操作，可能导致无法开机、数据损坏。**请务必备份 boot 镜像**。
- 系统必须与之匹配：本构建针对 ColorOS 16 / PJX110_16.0.5.1001（6.1.141）。系统 OTA 升级后内核基线可能变化，需重新构建。
- 若构建失败，优先检查 SukiSU / SUSFS 上游分支是否刚更新且尚未互适配；可在工作流中把 `SUSFS_BRANCH` 换成 `gki-android14-6.1-dev` 尝试。
- 如需支持其它机型，修改工作流顶部 `env` 中的 `CPU` / `CPUD` / `FEIL` / `KERNEL_VERSION` / `ANDROID_VERSION` / `SUSFS_BRANCH` 即可。

## 参考与致谢

- [ShirkNeko/Action_OnePlus_MKSU_SUSFS](https://github.com/ShirkNeko/Action_OnePlus_MKSU_SUSFS)
- [OnePlusOSS/kernel_manifest](https://github.com/OnePlusOSS/kernel_manifest)（一加开源内核）
- [SukiSU-Ultra/SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- [simonpunk/susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu)（SUSFS）
- [ShirkNeko/SukiSU_patch](https://github.com/ShirkNeko/SukiSU_patch)
- [Numbersf/AnyKernel3](https://github.com/Numbersf/AnyKernel3)
