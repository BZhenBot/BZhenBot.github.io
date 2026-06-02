---
layout: single
title: "浙江省人工智能竞赛"
permalink: /projects/zhejiang-ai-competition/
author_profile: true
---

{% include base_path %}

[返回 Projects](/projects/)

**基于 YOLOv5 的人群计数与异常聚集预警系统**

浙江省人工智能竞赛省级二等奖，算法负责人。项目面向高密度公共场景，基于 YOLOv5 完成行人检测与计数，并结合中心点聚类识别异常聚集区域。

创新点
======

- 将 YOLOv5 从 COCO 多类别检测定制为单类 `person` 检测任务，减少分类干扰，让模型更聚焦行人定位。
- 针对人体目标的纵向长宽比例重新设计 anchors，使检测先验更贴合行人尺度分布。
- 尝试引入 C3TR 模块增强全局上下文建模，提升拥挤、遮挡和远距离场景下的检测适应性。
- 探索 FasterNet/PConv/FasterNeXt 风格轻量化结构，在检测精度和推理效率之间寻找更合适的平衡。
- 将检测框转换为人员中心点，并引入 DBSCAN 思想进行空间聚类，把目标检测结果进一步转化为异常聚集区域判断。

我负责的内容
======

- 负责算法方案调研。
- 完成单类行人检测训练配置适配，包括类别收敛、anchors 调整和相关参数配置。
- 设计并实现基于人员中心点的异常聚集分析流程。
- 参与 C3TR、FasterNet/PConv 等结构优化实验，并比较不同改造方案在任务中的适配性。
