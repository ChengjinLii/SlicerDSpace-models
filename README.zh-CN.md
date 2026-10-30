<div align="center">

# SlicerDSpace 运行资源

[代码](https://github.com/ChengjinLii/SlicerDSpace) | [资源分组](#资源分组) | [授权说明](RESOURCE_NOTICES.md) | [English](README.md)

</div>

---

`SlicerDSpace-models` 不只保存权重：
模型、图谱、模板及必要的大型非代码运行资源均在此分组管理。
默认 core 仍只有原有 31 个解剖资源；其他资源按需获取，不随插件代码全量下载。

---

## 资源分组

| 路径 | 内容 |
| --- | --- |
| `surface/`、`segmentation/`、`parcellation/` | 原有 DDSurfer、DDSeg、DDParcel 权重及模板 |
| `brain_mask/pnl_cnn/` | 三方向权重、网络结构、完整原许可；IIT 模板仅本地保留且 Git 忽略 |
| `brain_mask/synthstrip/` | 作者提供的版本 1 主模型，采用 CC BY 4.0 许可选项 |
| `bundles/tractcloud/` | 最小推理权重、参数、308 字节图谱参考、原许可 |
| `bundles/wma/archives/` | 两个固定版本的 ORG 官方 ZIP；不重复提交解压目录 |
| `bundles/wma/east-west/` | 东西方成人图谱的按需运行包、署名、校验清单 |
| `bundles/wma/naba/` | 新生儿/成人图谱的按需运行包、署名、校验清单 |

PNL 与 TractCloud 的权重不能笼统改标为本项目 BSD 许可；须保留各自完整协议与引用。
WMA/ORG 官方 Zenodo 记录采用 CC BY 4.0；同时保留上游附带的 Slicer 协议。
**IIT 模板未经书面许可不得上传网站或其他电子媒介**，因此它不是本仓库的镜像内容。
本地拥有或成功校验文件，并不等于取得再分发许可。

---

## 清单与获取

`asset_mirror_manifest.json` 继续描述原有 core，避免覆盖已有发布记录。
可选资源单独记录为 `optional_assets_mirror_manifest.json` 与
`optional_assets_mirror_index.tsv`。代码仓库中的
`models/external_assets_manifest.json` 保存可信摘要、大小、来源及分发策略。

14 个可选资源已通过提交 `11167a5` 推送，其中 8 个为 LFS 二进制对象。
公开资源镜像支持匿名 URL 下载，代码仓库可继续保持私有；本轮没有修改仓库可见性。
干净目录中匿名下载 TractCloud 四个推理文件，大小及 SHA256 均已通过。
另外从 GitHub 全新检出后独立回读，14 个可选资源的大小及 SHA256 全部通过，无未解析的 LFS 指针。

从代码仓库验证本地目录：

```bash
python3 scripts/download_model_assets.py \
  --manifest models/external_assets_manifest.json \
  --profile mirrorable-inference \
  --asset-root /path/to/SlicerDDSurfer-models --verify-only
```

完整 PNL 本地检查使用 `--profile pnl-cnn`；该组合包括受限 IIT 模板，
导出工具会拒绝把整个组合打入镜像。WMA 解压内容可从受校验 ZIP 重新生成，
本地索引记录每个解压文件的 SHA256。

仅下载当前需要的可选资源，无需安装 Git 或 Git LFS 客户端：

```bash
python3 scripts/setup_optional_assets.py --profiles tractcloud wma \
  --asset-root /path/to/resources --execute --accept-terms
```

GUI 中使用“环境与安装 > 准备所选分析环境”，确认许可后后台下载、校验并自动保存路径。
下载权重和图谱不等于安装对应的算法运行环境。

---

## 不纳入的文件

Python/Conda 环境、CUDA 库、FSL/MRtrix/FreeSurfer 程序、安装器缓存仍由独立 runtime 管理。
不迁入用户 DWI、临床数据、训练/测试数据集、分析输出及逐被试生成的拟合字典。
资源齐全也不代表对应软件、GPU 或科学 QC 已通过。

## 三种 WMA 图谱

`--wma-atlas org|east-west|naba` 选择图谱，默认仍是 ORG。
[East-West 论文](https://doi.org/10.1038/s41597-024-03624-2)对应东西方成人人群；
[NABA 论文](https://doi.org/10.1016/j.fmre.2025.12.030)对应新生儿和成人，不笼统覆盖所有儿童。
新增两套运行包使用小于等于 512 MiB 的 Git LFS 分片；仅下载所选 profile
`wma-east-west` 或 `wma-naba`。原始数据字节不变，保留配准图谱、谱图谱、全部 800 个参考簇、
半球归属文件与束定义 MRML。`ATLAS.json` 记录来源与原始压缩包校验值，
`extracted_resource_index.json` 逐文件记录 SHA256；解压树只在本地保留。

离线镜像可先克隆元数据，再只取需要的图谱分片。以下以 NABA 为例；East-West 将路径中的
`naba` 改为 `east-west`：

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/ChengjinLii/SlicerDSpace-models.git
git -C SlicerDSpace-models lfs pull --include='bundles/wma/naba/archives/*' --exclude=''
```

插件的准备工具也支持直接 HTTP 下载，无须克隆资源仓库；安装计划会列出下载与解压所需空间。
