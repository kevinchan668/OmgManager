# OmgManager — Sony Walkman OMGAUDIO 音乐库管理工具

**直接编辑播放器上的歌单，找回和迁移音乐，原地重整修复 OMGAUDIO 音乐库。**

OmgManager 是面向部分经典 Sony Network Walkman 的 Windows 桌面应用，围绕设备的 **OMGAUDIO 音乐数据库**提供音乐导入导出、设备播放列表直接编辑、M3U 工作流、独立音频转码及数据库维护，并公开相关格式研究成果。

**[English](README.md)** · **[安装说明](docs/installation.md)** · **[版本下载](https://github.com/kevinchan668/OmgManager/releases)** · **[英文 OMGAUDIO 研究](docs/research/README.md)**

> **公开范围：** 本阶段已提供 V9.3.13 可执行程序和技术文档，**暂不公开应用源代码**。OmgManager 为独立非官方项目，与 Sony 无隶属关系。

## OmgManager 的独特优势

OmgManager 不只是向 Walkman 传输歌曲，而是能够围绕**播放器内部已经存在的音乐库**开展管理——即使 PC 上已经没有当年 SonicStage 的本地音乐库及歌单。

### 核心特色功能

| 特色功能 | 实际价值 |
| --- | --- |
| **直接编辑播放器内部播放列表** | 不要求 PC 本地保留相同歌单，即可创建、重命名、删除、调整顺序及增删设备歌单曲目。解决传统 SonicStage 工作流必须先编辑 PC 歌单、再同步设备的限制。 |
| **PL 格式播放列表批量备份与恢复** | 使用 OmgManager 自建的 **PL 格式**备份和导入播放列表，特别适合拥有大量精心整理歌单的播放器。 |
| **根据 M3U 生成设备播放列表** | 如果 M3U 引用的歌曲已经存在于播放器音乐库，就可据此建立设备歌单；也支持基于 M3U 的歌曲导入流程。 |
| **从 OMA 封装中导出音乐** | 将播放器中受支持的 OMA 封装音乐还原为电脑可使用的音频格式，并可配合导出播放列表；实际能力受音源格式和保护机制限制。 |
| **跨型号音乐与歌单迁移** | 从旧 Walkman 导出音乐与歌单，再导入另一台受支持的机型，按照目标设备要求重新处理格式和数据库；**不是直接复制 OMGAUDIO 文件夹**。 |
| **不清空音乐库的数据重整修复** | 针对反复操作后可能累积的数据库偏差，在保留音乐内容的前提下，尝试原地重整数据文件和音乐文件，使结构趋近于“初始化音乐库后一次导入全部音乐和播放列表”的干净状态。操作前必须备份，具体能力受机型限制。 |
| **脱离实体播放器初始化 OMGAUDIO** | 无需连接 Walkman，即可在 PC 任意目录创建 OMGAUDIO 音乐库，便于离线准备与研究；不代表该结果对所有设备都有效。 |
| **与 SonicStage 相互接管的逆向成果** | 已测试的操作中，OmgManager 修改后的库能被 SonicStage 接受而无需强制修复或初始化，即使数据字节和时间戳不与 Sony 完全相同；具体兼容边界因机型而异。 |

**关键差异：** 如果播放器里还保留十几年前的歌单，但电脑上的 SonicStage 歌单早已丢失，OmgManager 仍以**设备上现存的歌单**作为直接编辑对象，而不是要求先恢复过去的 PC 音乐库。

## 常规音乐管理功能

| 功能 | 说明 |
| --- | --- |
| 音乐库浏览 | 按歌曲、专辑、艺术家和播放列表管理受支持的 OMGAUDIO 音乐库。 |
| 音乐导入与删除 | 导入兼容音频，删除歌曲并协调更新相关数据库结构。 |
| M3U 工作流 | 根据 M3U 导入歌曲，或利用播放器现有歌曲创建歌单。 |
| 独立音频转码 | 不连接播放器即可转换音频；默认支持 MP3，ATRAC 编码需要用户另外配置兼容工具。 |
| 设备数据库维护 | 提供检查、初始化、重整修复及其他维护功能；操作前必须备份。 |

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
