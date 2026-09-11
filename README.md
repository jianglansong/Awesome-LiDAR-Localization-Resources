# Awesome-LiDAR-Localization-Resources
![ LiDAR Localizatio](./image/header.png)

这是来自中国南京东南大学的激光雷达定位调资料库。
该资料库旨在为对激光雷达定位感兴趣的研究人员或者从业者提供精选资源，包括：雷达选型、标定与预处理工具、定位算法和开源数据集。
该工作受到东南大学中英智能移动研究中心支持。
如果觉得这个仓库有帮助，请考虑星号 ⭐收藏或者👋分享。


> [!NOTE]  
> 若发现缺失资源或有新的发现，欢迎提交拉取请求或开启问题。我们很高兴能与您合作打造这个仓库。


> 表格中的价格档、测距与线数为公开资料量级，仅用于选型对照，不构成采购建议。型号迭代很快，落地前请以厂商最新规格书与车规认证为准。
## 目录

- [1. 激光雷达选购指南](#1-激光雷达选购指南)
  - [技术路线对照](#技术路线对照)
  - [选型要点](#选型要点)
- [2. 标定与预处理开源工具](#2-标定与预处理开源工具)
  - [标定 · 车载](#标定--车载)
  - [标定 · V2X](#标定--v2x)
  - [预处理 · 通用](#预处理--通用)
  - [预处理 · 车端](#预处理--车端)
  - [预处理 · 路侧](#预处理--路侧)
- [3. 激光雷达定位算法](#3-激光雷达定位算法)
  - [车载定位 · LiDAR 里程计](#车载定位--lidar-里程计)
  - [车载定位 · SLAM](#车载定位--slam)
  - [车载定位 · 高精地图定位](#车载定位--高精地图定位)
  - [路侧定位 · 单帧位姿获取](#路侧定位--单帧位姿获取)
  - [路侧定位 · 连续定位构建](#路侧定位--连续定位构建)
  - [V2X 定位 · 车车协同](#v2x-定位--车车协同)
  - [V2X 定位 · 车路协同](#v2x-定位--车路协同)
  - [V2X 定位 · 车路云协同](#v2x-定位--车路云协同)
- [4. 定位数据集](#4-定位数据集)
  - [车载真实](#车载真实)
  - [车载仿真](#车载仿真)
  - [路侧真实](#路侧真实)
  - [车路真实](#车路真实)
  - [车路仿真](#车路仿真)
  - [车车仿真](#车车仿真)
  - [车车真实](#车车真实)
- [5. 白皮书、标准与社区](#5-白皮书标准与社区)
  - [政策与产业白皮书](#政策与产业白皮书)
  - [国际组织与技术报告](#国际组织与技术报告)
  - [开源自动驾驶与协同感知社区](#开源自动驾驶与协同感知社区)
- [Contributing](#contributing)
- [License](#license)

---

## 1. 激光雷达选购指南

定位系统首先被传感器形态约束：周视还是前向、远场密度、近处补盲、车规与路侧安装条件，都会决定后续标定、里程计和地图匹配怎么做。下表按扫描机理整理常见量产路线。

### 技术路线对照

| 类型 | 扫描方式 | 典型 FOV | 线数 / 等效线数 | 测距 @10%（量级） | 点云形态 | 车载主雷达 | 车载补盲 | 路侧 | 价格量级 | 代表品牌 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 机械式 | 收发整体旋转 | H ≈ 360°；V 由线数决定 | 16–128 真线 | 100–200 m；旗舰可 >200 m；近距周视约 20–40 m | 环状分层，远场径向变稀 | 可用（Robotaxi / 高阶示范常见） | 较少作纯补盲 | 常用 | 万元级 | 禾赛、Ouster、速腾 |
| 半固态 · 转镜 | 收发固定，转镜扫描 | 前向约 120° × 20°–25° | 量产真 128 线；旗舰 192–256 线量级 | 量产 150–230 m；部分 250–300 m | 前向扇区，远场变稀，单视场无接缝 | 主流前向长距 | 一般不单独作超近补盲 | 部分路口/杆顶前向 | 千元级 | 禾赛、图达通、华为、Valeo |
| 半固态 · MEMS | 收发固定，微振镜扫描 | 前向约 120° × 25° | 等效约 120–200 线；ROI 可加密 | 量产 150–200 m | 前向扇区，远场变稀 | 前向主雷达 | 视产品而定 | 较少 | 千元级 | 速腾、Innoviz |
| 半固态 · 棱镜 | 收发固定，棱镜 / 光楔扫描 | 前向扇区，产品差异大 | 如 Livox HAP 等效约 144 线 | 约 90–150 m | 非重复 / 花瓣扫描，远场变稀、边缘更疏 | 可用（需适应非重复扫描） | 可用 | 较少作标准路口雷达 | 千元级 | Livox |
| 全固态 · Flash | 无宏观运动，面阵照明 / 电扫描 | 近似相机；补盲可至 100° × 75° 乃至更宽 | 面阵折算等效数十至一两百线 | 约 20–50 m（补盲主流约 20–30 m） | 近场较规则深度图，远场迅速变差 | 不作长距主雷达 | 适合 | 近距补盲 / 路口近场 | 千元级 | 禾赛、速腾、华为 |
| 全固态 · OPA | 电控波束偏转 | 可编程扇区 | 由阵列通道数决定 | 车规长距仍在发展 | 取决于阵列与扫描策略 | 尚少量产上车 | — | 研究 / 试点 | — | Analog Photonics、洛微 |

**读表说明**

- 「车载主雷达 / 补盲 / 路侧」是常见部署，不是硬限制。机械式 360° 仍广泛用于 Robotaxi、高精建图和路侧杆顶；乘用车前向量产以转镜 / MEMS 半固态为主。
- 路侧更关心：安装高度与俯仰、多雷达拼视场、长期在线标定、雨雪脏污与维护，而不是车规震动曲线。
- Livox 类非重复扫描对传统「按线号去畸变」的里程计不友好，需要时间戳级去畸变或专用前端。
- OPA / 纯固态长距仍处发展期，写入表中是为了技术地图完整，不建议作为当前定位基线的唯一传感器假设。

### 选型要点

1. **先定任务再定雷达**  
   高精建图与全局定位需要足够的结构特征与稳定外参；前向高速定位更吃远场分辨率；路口路侧跟踪吃的是近场完整性与多传感器时间同步。
2. **驱动与时间戳**  
   优先确认官方 ROS / ROS 2 驱动、点云时间戳是逐点还是逐帧、是否输出 IMU 内参。这直接决定能否跑 FAST-LIO2、LIO-SAM、KISS-ICP 一类开源栈。
3. **标定闭环**  
   多雷达、雷达–IMU、雷达–相机、V2I 外参要在选型阶段就规划标定场地与工具（见第 2 章），而不是交付后再补。
4. **路侧与车载不要混用同一套假设**  
   路侧雷达近似静态，适合做高精背景图与目标跟踪；车载雷达是运动平台，必须处理运动畸变与在线外参漂移。

厂商官网与 SDK（选型时从这里核对最新型号，避免把过时线数写进方案）：

- [Hesai（禾赛）](https://www.hesaitech.com/)
- [RoboSense（速腾聚创）](https://www.robosense.ai/)
- [Livox](https://www.livoxtech.com/)
- [Innovusion（图达通）](https://www.innovusion.com/)
- [Ouster](https://ouster.com/)
- [Innoviz](https://innoviz.tech/)
- [Valeo SCALA](https://www.valeo.com/)
- [华为智能汽车解决方案](https://www.huawei.com/cn/huaweitech/)

---

## 2. 标定与预处理开源工具

### 标定 · 车载

| 软件 | 论文 | 开源库 |
| --- | --- | --- |
| LI-Calib | [doi.org/10.1109/IROS45743.2020.9341127](https://doi.org/10.1109/IROS45743.2020.9341127) | [APRIL-ZJU/lidar_IMU_calib](https://github.com/APRIL-ZJU/lidar_IMU_calib) |
| LI-Init | [doi.org/10.1109/IROS47612.2022.9981963](https://doi.org/10.1109/IROS47612.2022.9981963) | [hku-mars/LiDAR_IMU_Init](https://github.com/hku-mars/LiDAR_IMU_Init) |
| Multi-LiCa | [arxiv.org/abs/2501.11088](https://arxiv.org/abs/2501.11088) | [TUMFTM/Multi_LiCa](https://github.com/TUMFTM/Multi_LiCa) |
| FAST-Calib | [arxiv.org/abs/2507.17210](https://arxiv.org/abs/2507.17210) | [hku-mars/FAST-Calib](https://github.com/hku-mars/FAST-Calib) |
| direct visual-lidar calibration | [doi.org/10.1109/ICRA48891.2023.10161464](https://doi.org/10.1109/ICRA48891.2023.10161464) | [koide3/direct_visual_lidar_calibration](https://github.com/koide3/direct_visual_lidar_calibration) |
| iKalibr | [arxiv.org/abs/2407.11420](https://arxiv.org/abs/2407.11420) | [Unsigned-Long/iKalibr](https://github.com/Unsigned-Long/iKalibr) |
| OpenCalib | [doi.org/10.1016/j.simpa.2022.100393](https://doi.org/10.1016/j.simpa.2022.100393) | [PJLab-ADG/SensorsCalibration](https://github.com/PJLab-ADG/SensorsCalibration) |

### 标定 · V2X

| 软件 | 论文 | 开源库 |
| --- | --- | --- |
| V2I-Calib / V2X-Reg++ | [arxiv.org/abs/2407.10195](https://arxiv.org/abs/2407.10195) | [MassimoQu/v2i-calib](https://github.com/MassimoQu/v2i-calib) |
| MamV2XCalib | [arxiv.org/abs/2507.23595](https://arxiv.org/abs/2507.23595) | [zhuyaoye/MamV2XCalib](https://github.com/zhuyaoye/MamV2XCalib) |

### 预处理 · 通用

| 软件 | 论文 | 开源库 |
| --- | --- | --- |
| PCL | [doi.org/10.1109/ICRA.2011.5980567](https://doi.org/10.1109/ICRA.2011.5980567) | [PointCloudLibrary/pcl](https://github.com/PointCloudLibrary/pcl) |
| Open3D | [arxiv.org/abs/1801.09847](https://arxiv.org/abs/1801.09847) | [isl-org/Open3D](https://github.com/isl-org/Open3D) |
| OpenPCDet | — | [open-mmlab/OpenPCDet](https://github.com/open-mmlab/OpenPCDet) |
| MMDetection3D | — | [open-mmlab/mmdetection3d](https://github.com/open-mmlab/mmdetection3d) |

### 预处理 · 车端

| 软件 | 论文 | 开源库 |
| --- | --- | --- |
| Autoware distortion_corrector | — | [autowarefoundation/autoware_universe](https://github.com/autowarefoundation/autoware_universe) |
| VICET | [doi.org/10.1109/LRA.2023.3346753](https://doi.org/10.1109/LRA.2023.3346753) | [mcdermatt/VICET](https://github.com/mcdermatt/VICET) |
| Removert | [doi.org/10.1109/IROS45743.2020.9341564](https://doi.org/10.1109/IROS45743.2020.9341564) | [gisbi-kim/removert](https://github.com/gisbi-kim/removert) |
| ERASOR | [doi.org/10.1109/LRA.2021.3061353](https://doi.org/10.1109/LRA.2021.3061353) | [LimHyungTae/ERASOR](https://github.com/LimHyungTae/ERASOR) |
| Patchwork++ | [doi.org/10.1109/IROS47612.2022.9981568](https://doi.org/10.1109/IROS47612.2022.9981568) | [url-kaist/patchwork-plusplus](https://github.com/url-kaist/patchwork-plusplus) |

### 预处理 · 路侧

| 软件 | 论文 | 开源库 |
| --- | --- | --- |
| PDAL | — | [PDAL/PDAL](https://github.com/PDAL/PDAL) |
| CloudCompare + CSF | [doi.org/10.3390/rs8060501](https://doi.org/10.3390/rs8060501) | [jianboqi/CSF](https://github.com/jianboqi/CSF) |

---

## 3. 激光雷达定位算法

未提供开源实现的条目记为 `-`。年份以论文发表信息为准。

### 车载定位 · LiDAR 里程计

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| LOAM | 2014 | RSS | [doi.org/10.15607/RSS.2014.X.007](https://doi.org/10.15607/RSS.2014.X.007) | [laboshinl/loam_velodyne](https://github.com/laboshinl/loam_velodyne) |
| F-LOAM | 2021 | IROS | [doi.org/10.1109/IROS51168.2021.9636655](https://doi.org/10.1109/IROS51168.2021.9636655) | [wh200720041/floam](https://github.com/wh200720041/floam) |
| KISS-ICP | 2023 | IEEE RA-L | [doi.org/10.1109/LRA.2023.3236571](https://doi.org/10.1109/LRA.2023.3236571) | [PRBonn/kiss-icp](https://github.com/PRBonn/kiss-icp) |
| CT-ICP | 2022 | ICRA | [doi.org/10.1109/ICRA46639.2022.9811849](https://doi.org/10.1109/ICRA46639.2022.9811849) | [jedeschaud/ct_icp](https://github.com/jedeschaud/ct_icp) |
| FAST-LIO2 | 2022 | IEEE T-RO | [doi.org/10.1109/TRO.2022.3141876](https://doi.org/10.1109/TRO.2022.3141876) | [hku-mars/FAST_LIO](https://github.com/hku-mars/FAST_LIO) |
| LIO-SAM | 2020 | IROS | [doi.org/10.1109/IROS45743.2020.9341176](https://doi.org/10.1109/IROS45743.2020.9341176) | [TixiaoShan/LIO-SAM](https://github.com/TixiaoShan/LIO-SAM) |
| PWCLO-Net | 2021 | CVPR | [doi.org/10.1109/CVPR46437.2021.01565](https://doi.org/10.1109/CVPR46437.2021.01565) | [IRMVLab/PWCLONet](https://github.com/IRMVLab/PWCLONet) |
| ININ-LIO | 2025 | IEEE TIM | [doi.org/10.1109/TIM.2025.3546399](https://doi.org/10.1109/TIM.2025.3546399) | - |

### 车载定位 · SLAM

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| LeGO-LOAM | 2018 | IROS | [doi.org/10.1109/IROS.2018.8593950](https://doi.org/10.1109/IROS.2018.8593950) | [RobustFieldAutonomyLab/LeGO-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM) |
| Cartographer | 2016 | ICRA | [doi.org/10.1109/ICRA.2016.7487258](https://doi.org/10.1109/ICRA.2016.7487258) | [cartographer-project/cartographer](https://github.com/cartographer-project/cartographer) |
| Scan Context | 2018 | IROS | [doi.org/10.1109/IROS.2018.8593953](https://doi.org/10.1109/IROS.2018.8593953) | [irapkaist/scancontext](https://github.com/irapkaist/scancontext) |
| STD | 2023 | ICRA | [doi.org/10.1109/ICRA48891.2023.10160434](https://doi.org/10.1109/ICRA48891.2023.10160434) | [hku-mars/STD](https://github.com/hku-mars/STD) |
| SuMa++ | 2019 | IROS | [doi.org/10.1109/IROS40897.2019.8967704](https://doi.org/10.1109/IROS40897.2019.8967704) | [PRBonn/semantic_suma](https://github.com/PRBonn/semantic_suma) |
| PIN-SLAM | 2024 | ICRA | [doi.org/10.1109/ICRA57147.2024.10610252](https://doi.org/10.1109/ICRA57147.2024.10610252) | [PRBonn/PIN_SLAM](https://github.com/PRBonn/PIN_SLAM) |
| LVI-SAM | 2021 | ICRA | [doi.org/10.1109/ICRA48506.2021.9561996](https://doi.org/10.1109/ICRA48506.2021.9561996) | [TixiaoShan/LVI-SAM](https://github.com/TixiaoShan/LVI-SAM) |
| FAST-LIVO | 2022 | IROS | [doi.org/10.1109/IROS47612.2022.9981107](https://doi.org/10.1109/IROS47612.2022.9981107) | [hku-mars/FAST-LIVO](https://github.com/hku-mars/FAST-LIVO) |

### 车载定位 · 高精地图定位

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| hdl_global_localization | 2019 | Int. J. Adv. Robot. Syst. | [doi.org/10.1177/1729881419841532](https://doi.org/10.1177/1729881419841532) | [koide3/hdl_global_localization](https://github.com/koide3/hdl_global_localization) |
| Autoware NDT | 2015 | IEEE Micro | [doi.org/10.1109/MM.2015.133](https://doi.org/10.1109/MM.2015.133) | [autowarefoundation/autoware](https://github.com/autowarefoundation/autoware) |
| Apollo 定位 | 2018 | ICRA | [doi.org/10.1109/ICRA.2018.8461251](https://doi.org/10.1109/ICRA.2018.8461251) | [ApolloAuto/apollo](https://github.com/ApolloAuto/apollo) |
| Levinson 等 | 2007 | RSS | [doi.org/10.15607/RSS.2007.III.016](https://doi.org/10.15607/RSS.2007.III.016) | - |
| Wolcott 等 | 2015 | ICRA | [doi.org/10.1109/ICRA.2015.7139582](https://doi.org/10.1109/ICRA.2015.7139582) | - |
| PointNetVLAD | 2018 | CVPR | [doi.org/10.1109/CVPR.2018.00470](https://doi.org/10.1109/CVPR.2018.00470) | [mikacuy/pointnetvlad](https://github.com/mikacuy/pointnetvlad) |
| OverlapTransformer | 2022 | IEEE RA-L | [doi.org/10.1109/LRA.2022.3178797](https://doi.org/10.1109/LRA.2022.3178797) | [haomo-ai/OverlapTransformer](https://github.com/haomo-ai/OverlapTransformer) |
| L3-Net | 2019 | CVPR | [doi.org/10.1109/CVPR.2019.00655](https://doi.org/10.1109/CVPR.2019.00655) | - |
| LCDNet | 2022 | IEEE T-RO | [doi.org/10.1109/TRO.2022.3150683](https://doi.org/10.1109/TRO.2022.3150683) | [robot-learning-freiburg/LCDNet](https://github.com/robot-learning-freiburg/LCDNet) |

### 路侧定位 · 单帧位姿获取

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| Zhao 等 | 2019 | Transp. Res. Part C | [doi.org/10.1016/j.trc.2019.01.007](https://doi.org/10.1016/j.trc.2019.01.007) | - |
| SECOND | 2018 | Sensors | [doi.org/10.3390/s18103337](https://doi.org/10.3390/s18103337) | [traveller59/second.pytorch](https://github.com/traveller59/second.pytorch) |
| PointPillars | 2019 | CVPR | [doi.org/10.1109/CVPR.2019.01298](https://doi.org/10.1109/CVPR.2019.01298) | [nutonomy/second.pytorch](https://github.com/nutonomy/second.pytorch) |
| CenterPoint | 2021 | CVPR | [doi.org/10.1109/CVPR46437.2021.01155](https://doi.org/10.1109/CVPR46437.2021.01155) | [tianweiy/CenterPoint](https://github.com/tianweiy/CenterPoint) |
| AFRNet | 2024 | Remote Sensing | [doi.org/10.3390/rs16050782](https://doi.org/10.3390/rs16050782) | - |
| CetrRoad | 2023 | Sustainability | [doi.org/10.3390/su15032628](https://doi.org/10.3390/su15032628) | - |

### 路侧定位 · 连续定位构建

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| Ma 等 | 2024 | J. Transp. Eng. Part A | [doi.org/10.1061/JTEPBS.TEENG-7990](https://doi.org/10.1061/JTEPBS.TEENG-7990) | - |
| SORT | 2016 | ICIP | [doi.org/10.1109/ICIP.2016.7533003](https://doi.org/10.1109/ICIP.2016.7533003) | [abewley/sort](https://github.com/abewley/sort) |
| Lin 等 | 2023 | Sensors | [doi.org/10.3390/s23198143](https://doi.org/10.3390/s23198143) | - |
| RCooper | 2024 | CVPR | [doi.org/10.1109/CVPR52733.2024.02109](https://doi.org/10.1109/CVPR52733.2024.02109) | [AIR-THU/DAIR-RCooper](https://github.com/AIR-THU/DAIR-RCooper) |
| RCTracker | 2025 | Springer 会议论文 | [doi.org/10.1007/978-981-96-7441-1_10](https://doi.org/10.1007/978-981-96-7441-1_10) | - |
| 郭紫祎等 | 2024 | 测绘通报 | - | - |
| Bench-RNR | 2025 | arXiv | [arxiv.org/abs/2509.15583](https://arxiv.org/abs/2509.15583) | [sjtu-cyberc3/BenchRNR](https://github.com/sjtu-cyberc3/BenchRNR) |

### V2X 定位 · 车车协同

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| DiSCo-SLAM | 2022 | IEEE RA-L | [doi.org/10.1109/LRA.2021.3138156](https://doi.org/10.1109/LRA.2021.3138156) | [RobustFieldAutonomyLab/DiSCo-SLAM](https://github.com/RobustFieldAutonomyLab/DiSCo-SLAM) |
| V2VNet | 2020 | ECCV | [doi.org/10.1007/978-3-030-58526-6_36](https://doi.org/10.1007/978-3-030-58526-6_36) | [coperception/coperception](https://github.com/coperception/coperception) |
| Where2comm | 2022 | NeurIPS | [arxiv.org/abs/2209.12836](https://arxiv.org/abs/2209.12836) | [MediaBrain-SJTU/Where2comm](https://github.com/MediaBrain-SJTU/Where2comm) |
| Soatti 等 | 2018 | IEEE T-ITS | [doi.org/10.1109/TITS.2018.2794405](https://doi.org/10.1109/TITS.2018.2794405) | - |
| CLS-MPNN | 2024 | IEEE T-SP | [doi.org/10.1109/TSP.2024.3374510](https://doi.org/10.1109/TSP.2024.3374510) | - |

### V2X 定位 · 车路协同

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| VRF | 2024 | MobiSys | [doi.org/10.1145/3643832.3661874](https://doi.org/10.1145/3643832.3661874) | [nsslofficial/VRF](https://github.com/nsslofficial/VRF) |
| VI-Eye | 2021 | MobiCom | [doi.org/10.1145/3447993.3483276](https://doi.org/10.1145/3447993.3483276) | - |
| PillarGrid | 2022 | arXiv / ITSC | [arxiv.org/abs/2203.06319](https://arxiv.org/abs/2203.06319) | - |
| Map4comm | 2026 | Information Fusion | [doi.org/10.1016/j.inffus.2025.103567](https://doi.org/10.1016/j.inffus.2025.103567) | - |
| Jiang 等 | 2024 | IEEE ITSC | [doi.org/10.1109/ITSC58415.2024.10595216](https://doi.org/10.1109/ITSC58415.2024.10595216) | - |
| 黄文锦等 | 2020 | 浙江大学学报(工学版) | [doi.org/10.3785/j.issn.1008-973X.2020.07.015](https://doi.org/10.3785/j.issn.1008-973X.2020.07.015) | - |
| Zheng 等 | 2025 | Automotive Innovation | [doi.org/10.1007/s42154-024-00300-6](https://doi.org/10.1007/s42154-024-00300-6) | - |
| Bai 等 | 2023 | IEEE T-ITS | [doi.org/10.1109/TITS.2023.3271391](https://doi.org/10.1109/TITS.2023.3271391) | - |

### V2X 定位 · 车路云协同

| 方法 | 年份 | 期刊 / 会议 | 论文 | 开源资源 |
| --- | --- | --- | --- | --- |
| Andert 等 | 2025 | IEEE/ION PLANS | [doi.org/10.1109/PLANS61210.2025.11028497](https://doi.org/10.1109/PLANS61210.2025.11028497) | - |
| C2TAM | 2014 | Robot. Auton. Syst. | [doi.org/10.1016/j.robot.2013.11.007](https://doi.org/10.1016/j.robot.2013.11.007) | [lriazuelo/c2tam](https://github.com/lriazuelo/c2tam) |
| Cao 等 | 2023 | IROS | [doi.org/10.1109/IROS55552.2023.10341513](https://doi.org/10.1109/IROS55552.2023.10341513) | - |

---

## 4. 定位数据集

同一论文 / 同一组织发布的系列数据集会在「开源地址」中指向共同仓库或门户；使用前请核对许可协议与是否需要申请。

### 车载真实

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| KITTI | [Geiger et al., CVPR 2012](https://www.cvlibs.net/publications/Geiger2012CVPR.pdf) | [cvlibs.net/datasets/kitti](http://www.cvlibs.net/datasets/kitti/) |
| nuScenes | [arxiv.org/abs/1903.11027](https://arxiv.org/abs/1903.11027) | [nutonomy/nuscenes-devkit](https://github.com/nutonomy/nuscenes-devkit) |
| Waymo Open | [arxiv.org/abs/1912.04838](https://arxiv.org/abs/1912.04838) | [waymo-research/waymo-open-dataset](https://github.com/waymo-research/waymo-open-dataset) |
| Oxford RobotCar | [RobotCar IJRR](https://robotcar-dataset.robots.ox.ac.uk/images/robotcar_ijrr.pdf) | [ori-mrg/robotcar-dataset-sdk](https://github.com/ori-mrg/robotcar-dataset-sdk) |
| MulRan | [gskim-2020-icra](https://irap.kaist.ac.kr/publications/gskim-2020-icra.pdf) | [sites.google.com/view/mulran-pr](https://sites.google.com/view/mulran-pr) |
| Boreas | [arxiv.org/abs/2203.10168](https://arxiv.org/abs/2203.10168) | [utiasASRL/pyboreas](https://github.com/utiasASRL/pyboreas) |
| HeLiPR | [arxiv.org/abs/2309.14590](https://arxiv.org/abs/2309.14590) | [minwoo0611/HeLiPR-File-Player](https://github.com/minwoo0611/HeLiPR-File-Player) |
| Apollo-SouthBay | [L3-Net, CVPR 2019](https://openaccess.thecvf.com/content_CVPR_2019/html/Lu_L3-Net_Towards_Learning_Based_LiDAR_Localization_for_Autonomous_Driving_CVPR_2019_paper.html) | [developer.apollo.auto/southbay](https://developer.apollo.auto/southbay.html) |
| Complex Urban | [project site](https://sites.google.com/view/complex-urban-dataset) | [irapkaist/file_player](https://github.com/irapkaist/file_player) |
| NCLT | [NCLT page](https://robots.engin.umich.edu/nclt/) | [robots.engin.umich.edu/nclt](https://robots.engin.umich.edu/nclt/) |
| Odyssey | [arxiv.org/abs/2512.14428](https://arxiv.org/abs/2512.14428) | [odyssey.uni-goettingen.de](https://odyssey.uni-goettingen.de/) |

### 车载仿真

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| KITTI-CARLA | [arxiv.org/abs/2109.00892](https://arxiv.org/abs/2109.00892) | [jedeschaud/kitti_carla_simulator](https://github.com/jedeschaud/kitti_carla_simulator) |
| SynLiDAR | [AAAI](https://ojs.aaai.org/index.php/AAAI/article/view/20183) | [xiaoaoran/SynLiDAR](https://github.com/xiaoaoran/SynLiDAR) |
| SHIFT | [arxiv.org/abs/2206.08367](https://arxiv.org/abs/2206.08367) | [SysCV/shift-dev](https://github.com/SysCV/shift-dev) |

### 路侧真实

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| BAAI-VANJEE | [arxiv.org/abs/2105.14370](https://arxiv.org/abs/2105.14370) | [data.baai.ac.cn](https://data.baai.ac.cn/data-set) |
| IPS300+ | [arxiv.org/abs/2106.02781](https://arxiv.org/abs/2106.02781) | [openmpd.com/column/IPS300](http://www.openmpd.com/column/IPS300) |
| DAIR-V2X-I | [arxiv.org/abs/2204.05575](https://arxiv.org/abs/2204.05575) | [AIR-THU/DAIR-V2X](https://github.com/AIR-THU/DAIR-V2X) |
| LUMPI | [IEEE](https://ieeexplore.ieee.org/document/9827157) | [St3ff3nBusch/LUMPI-SDK-Python](https://github.com/St3ff3nBusch/LUMPI-SDK-Python) |
| TUMTraf-I | [arxiv.org/abs/2306.10620](https://arxiv.org/abs/2306.10620) | [tum-traffic-dataset](https://github.com/tum-traffic-dataset) |
| TUMTraf-A9 | [IEEE](https://ieeexplore.ieee.org/document/9825466) | [tum-traffic-dataset](https://github.com/tum-traffic-dataset) |
| Bench-RNR | [arxiv.org/abs/2509.15583](https://arxiv.org/abs/2509.15583) | [sjtu-cyberc3/BenchRNR](https://github.com/sjtu-cyberc3/BenchRNR) |
| RCooper | [arxiv.org/abs/2403.10145](https://arxiv.org/abs/2403.10145) | [AIR-THU/DAIR-RCooper](https://github.com/AIR-THU/DAIR-RCooper) |

### 车路真实

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| DAIR-V2X-C | [arxiv.org/abs/2204.05575](https://arxiv.org/abs/2204.05575) | [AIR-THU/DAIR-V2X](https://github.com/AIR-THU/DAIR-V2X) |
| V2X-Seq | [CVPR 2023](https://openaccess.thecvf.com/content/CVPR2023/html/Yu_V2X-Seq_A_Large-Scale_Sequential_Dataset_for_Vehicle-Infrastructure_Cooperative_Perception_and_CVPR_2023_paper.html) | [AIR-THU/DAIR-V2X-Seq](https://github.com/AIR-THU/DAIR-V2X-Seq) |
| V2X-Real | [arxiv.org/abs/2403.16034](https://arxiv.org/abs/2403.16034) | [ucla-mobility/V2X-Real](https://github.com/ucla-mobility/V2X-Real) |
| TUMTraf-V2X | [arxiv.org/abs/2403.01316](https://arxiv.org/abs/2403.01316) | [tum-traffic-dataset](https://github.com/tum-traffic-dataset) |
| UrbanV2X | [arxiv.org/abs/2512.20224](https://arxiv.org/abs/2512.20224) | [polyu-taslab.github.io/UrbanV2X](https://polyu-taslab.github.io/UrbanV2X/) |
| HoloVIC | [arxiv.org/abs/2403.02640](https://arxiv.org/abs/2403.02640) | [holovic.net](https://holovic.net) |

### 车路仿真

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| V2X-Sim | [arxiv.org/abs/2202.08449](https://arxiv.org/abs/2202.08449) | [ai4ce/V2X-Sim](https://github.com/ai4ce/V2X-Sim) |
| V2XSet | [arxiv.org/abs/2203.10638](https://arxiv.org/abs/2203.10638) | [DerrickXuNu/OpenCOOD](https://github.com/DerrickXuNu/OpenCOOD) |
| DeepAccident | [arxiv.org/abs/2304.01168](https://arxiv.org/abs/2304.01168) | [DeepAccident](https://github.com/DeepAccident) |

### 车车仿真

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| OPV2V | [arxiv.org/abs/2109.07644](https://arxiv.org/abs/2109.07644) | [DerrickXuNu/OpenCOOD](https://github.com/DerrickXuNu/OpenCOOD) |

### 车车真实

| 数据集 | Paper | 开源地址 |
| --- | --- | --- |
| V2V4Real | [arxiv.org/abs/2303.07601](https://arxiv.org/abs/2303.07601) | [ucla-mobility/V2V4Real](https://github.com/ucla-mobility/V2V4Real) |
| LUCOOP | [IEEE](https://ieeexplore.ieee.org/document/10186693) | [data.uni-hannover.de/dataset/lucoop](https://data.uni-hannover.de/dataset/lucoop) |

---

## 5. 白皮书、标准与社区
除了一些论文，目前对于激光雷达定位也延申出来、、

定位算法论文解决的是「怎么估位姿」；白皮书和社区解决的是「系统怎么部署、和谁对齐、数据怎么流通」。下面按可公开获取的材料整理，偏工程落地，不收录付费咨询报告全文。

### 政策与产业白皮书

| 材料 | 发布方 | 和激光雷达定位的关系 | 入口 |
| --- | --- | --- | --- |
| 智能网联汽车「车路云一体化」应用试点通知（2024） | 工信部等五部门 | 试点城市路侧单元、C-V2X 终端、云控平台的建设边界 | [工信部政策文件检索](https://www.miit.gov.cn/) |
| 《车路云一体化系统白皮书》（2023） | CAICV / 国家智能网联汽车创新中心 | 车、路、云分层架构，路侧感知与高精度定位被写成系统能力 | 检索「CAICV 车路云一体化系统白皮书 2023」 |
| 《智能网联汽车「车路云一体化」规模建设与应用参考指南（1.0）》 | 国家智能网联汽车创新中心、中国信通院等 | 规模化建设路径，路侧激光雷达 / 毫米波 / 相机的部署语境 | [china-icv.cn](https://www.china-icv.cn/) 及相关 PDF |
| 《2024 车路云一体化实践应用白皮书》 | 中国汽车工程学会（CSAE）等 | 地方建设与运营案例，可对照示范区路侧感知方案 | CSAE / 智能网联汽车产业创新联盟渠道 |
| 《国家车联网产业标准体系建设指南（智能网联汽车）》 | 工信部等 | 网联赋能与单车智能并行的标准时间表 | 工信部公开文件 |
| 百度 Apollo 车路协同相关白皮书（如 2.0 公开解读） | 百度 Apollo | 路端连续感知、路口协同通行与降低单车传感器配置的工程叙事 | [Apollo 开放平台](https://developer.apollo.auto/) |

### 国际组织与技术报告

| 材料 | 组织 | 说明 | 入口 |
| --- | --- | --- | --- |
| C-V2X Use Cases and Service Level Requirements (Vol. I–III) | 5GAA | 协同场景与服务等级，定位精度常作为 SLR 出现 | [5gaa.org](https://5gaa.org/c-v2x-use-cases-and-service-level-requirements-2025/) |
| High-Accuracy Positioning for C-V2X | 5GAA | 专门讨论 C-V2X 高精度定位需求、挑战与示范 | [5GAA V2XHAP TR](https://5gaa.org/content/uploads/2021/02/5GAA_A-200118_TR_V2XHAP.pdf) |
| Evaluation of Radio-Based Positioning Enhancements for Automotive Use Cases | 5GAA | 无线通信增强定位与汽车用例 | [5GAA Publications](https://5gaa.org/publications/) |
| 3GPP TS 22.186 / TR 38.885 等 | 3GPP | V2X 业务与 NR-V2X 技术要求，协同定位的通信约束 | [3gpp.org](https://www.3gpp.org/) |
| ETSI C-ITS / Collective Perception (CPM) | ETSI | 集体感知消息，路侧 / 车载目标如何编码共享 | [ETSI](https://www.etsi.org/) |

### 开源自动驾驶与协同感知社区

| 社区 / 项目 | 定位相关能力 | 链接 |
| --- | --- | --- |
| Autoware Foundation | NDT / 点云地图定位、畸变校正、V2X（AutowareV2X / CPM） | [autoware.org](https://autoware.org/) · [文档：Localization](https://autowarefoundation.github.io/autoware-documentation/main/design/autoware-architecture-v1/components/localization/) · [autoware_universe](https://github.com/autowarefoundation/autoware_universe) |
| Baidu Apollo | 多传感器融合定位、南湾等公开数据、车路协同工程栈 | [ApolloAuto/apollo](https://github.com/ApolloAuto/apollo) · [开发者平台](https://developer.apollo.auto/) |
| OpenCOOD / OPV2V / V2XSet | 车车、车路协同感知训练框架，常被拿来做协同定位的感知前端 | [DerrickXuNu/OpenCOOD](https://github.com/DerrickXuNu/OpenCOOD) |
| DAIR-V2X / V2X-Seq / RCooper | 国内最完整的车路真实数据与路侧协同基准之一 | [AIR-THU/DAIR-V2X](https://github.com/AIR-THU/DAIR-V2X) |
| TUM Traffic Dataset | 路侧与 V2X 路口点云 / 相机序列 | [tum-traffic-dataset](https://github.com/tum-traffic-dataset) |
| UCLA Mobility Lab | V2V4Real、V2X-Real | [ucla-mobility](https://github.com/ucla-mobility) |
| OpenPCDet / MMDetection3D | 点云检测训练栈，路侧单帧目标位姿的常用底座 | [OpenPCDet](https://github.com/open-mmlab/OpenPCDet) · [mmdetection3d](https://github.com/open-mmlab/mmdetection3d) |
| HKU-MARS | FAST-LIO / FAST-LIVO / FAST-Calib / STD 等车载定位工具链 | [hku-mars](https://github.com/hku-mars) |
| PRBonn | KISS-ICP、SuMa++、PIN-SLAM | [PRBonn](https://github.com/PRBonn) |

路侧激光雷达协同定位的工程论文示例：

- [Accurate Cooperative Localization Utilizing LiDAR-equipped Roadside Infrastructure](https://arxiv.org/abs/2407.08384)
- [Cooperative Visual-LiDAR Extrinsic Calibration … A review](https://arxiv.org/abs/2405.10132)


## Contributing

欢迎补充：

- 新的开源标定 / 去畸变 / 动态点剔除工具
- 有公开实现或可复现实验的定位算法
- 带官方许可说明的数据集
- 仍可公开下载的白皮书与标准 PDF 链接

建议 PR 时保持现有表格列不变，并同时给出论文链接与仓库链接。失效链接请直接开 issue。

## License
本仓库的文档内容建议采用 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)。被引用的论文、代码与数据集仍遵循原作者协议。

