# OmgManager — Sony Walkman OMGAUDIO 音乐库管理工具

**让经典 Sony Walkman 的音乐管理更直接、更方便。**

OmgManager 是面向部分经典 Sony Network Walkman 的 Windows 桌面应用，围绕设备的 **OMGAUDIO 音乐数据库**提供音乐导入导出、设备播放列表直接编辑、M3U 工作流、独立音频转码及数据库维护，并公开相关格式研究成果。

**[English](README.md)** · **[安装说明](docs/installation.md)** · **[版本下载](https://github.com/kevinchan668/OmgManager/releases)** · **[英文 OMGAUDIO 研究](docs/research/README.md)**

> **公开范围：** 本阶段计划提供 V9.3.13 可执行程序和技术文档，**暂不公开应用源代码**。OmgManager 为独立非官方项目，与 Sony 无隶属关系。

## 主要功能

| 功能 | 说明 |
| --- | --- |
| 音乐库管理 | 按曲目、专辑、艺术家浏览，导入或删除支持的设备音乐。 |
| 直接编辑播放列表 | 创建、重命名、删除、调整顺序及编辑设备播放列表；支持应用 PL 格式的播放列表备份与恢复。 |
| M3U 导入 | 从电脑已有的 M3U 列表选择导入曲目，或对设备已有曲目建立列表。 |
| 音乐导出 | 将支持的音乐从设备导出到电脑；实际结果受格式和保护机制限制。 |
| 独立音频转码 | 默认支持 MP3 转码；仅配置兼容的 Sony 编码器后开放 ATRAC 编码。 |
| 数据库维护 | 无需连接实体播放器即可在 PC 上初始化 OMGAUDIO 文件夹，并提供检查、维护及部分 Simple Mode 场景处理。 |

## 界面截图

### 主界面 — 音乐库

![OmgManager 音乐库主界面](assets/screenshots/02%20Music%20Library.jpg)

*音乐库主界面。截图采集自 V9.3.12，当前正式发布的应用版本为 V9.3.13。*

### 播放列表、M3U 导入与音乐导出

| 设备播放列表管理 | 播放列表直接编辑 |
| --- | --- |
| ![播放列表](assets/screenshots/05%20Playlists.jpg) | ![编辑播放列表](assets/screenshots/08%20Edit%20Playlist%201.jpg) |

| M3U 音乐导入 | 音乐导出 |
| --- | --- |
| ![M3U 导入](assets/screenshots/07%20Import%20from%20M3U.jpg) | ![音乐导出](assets/screenshots/12%20Export%20Music.jpg) |

全部 13 张图片按主界面优先的顺序展示在 [完整截图页面](docs/screenshots.md)，包括专辑、艺术家、转码、数据库维护及启动界面。

## 下载及安装

当前正式发布版本为 **V9.3.13**。请从 [GitHub Releases](https://github.com/kevinchan668/OmgManager/releases/tag/v9.3.13) 下载 ZIP，解压后运行 `OmgManager.exe`。运行环境为 Windows 与 .NET Framework 4.8。

**修改、删除、导入或修复之前，请完整备份设备上的 OMGAUDIO 文件夹。**

### MP3 默认可用，ATRAC 编码器需要用户自行配置

**V9.3.13 发布包不包含 `psp_at3tool.exe`。**无需该文件即可使用 MP3 转码与不依赖 Sony 编码器的其他功能。只有检测到兼容编码器时才开放 ATRAC 转码。

合法持有对应工具的用户，可自行将文件放入 `OmgManager/Tool/psp_at3tool.exe`，然后重启程序。

```text
OmgManager/
├── OmgManager.exe
└── Tool/
    ├── ffmpeg.exe
    ├── lame.exe
    └── psp_at3tool.exe  （选装：用户自行提供）
```

第三方参考地址：[GitHub ATRAC Codec TOOL](https://github.com/scx567888/ATRAC-Codec-TOOL/blob/master/psp_at3tool.exe)。**这不是 Sony 官方渠道，也不构成下载、使用或再分发授权**，用户需要自行确认权利与许可。

## 设备配置

目前应用含 **NW-A608、NW-S638F、NW-S706F、NW-HD5** 对应的配置模块。配置存在不代表全部操作均经过相应设备的完整实机测试。详见 [设备兼容性说明](docs/device-compatibility.md)。NW-HD5 的 ATRAC 导入当前不作为支持功能宣传。

## 研究成果

项目公开 OMGAUDIO 音乐数据库结构研究，包括 OMA/EA3、ContentKey、TREE 分类树、GPLB/TPLB、TREE22 播放列表、TREE01 传输记录、文件修改时间与 Simple Mode 设备实验。

详细研究资料参阅 [英文研究文献库](docs/research/README.md)，包括 [五维兼容性模型](docs/research/multidimensional-compatibility.md)、[数据库物理结构与写回不变量](docs/research/structural-invariants.md)、[SonicStage 兼容性阈值](docs/research/sonicstage-compatibility-threshold.md)、[实验研究方法](docs/research/research-methodology.md)。文档区分实验观察、工程解释及尚待验证的假设，不将单一机型结论推广为普遍规则。

## 注意事项

数据库写回具有风险，必须先备份；并非所有设备固件或 DRM 保护内容都能得到相同结果。第三方音频工具须分别遵守其许可要求。本仓库当前不提供 C# 或 PowerShell 源代码，也不暗示软件已按开源许可证授权。

Sony、Walkman、SonicStage 等商标属于各自权利人。
