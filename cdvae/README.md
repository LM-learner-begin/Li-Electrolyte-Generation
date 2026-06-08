#  CDVAE for Solid-State Electrolyte Generation
**基于晶体扩散变分自编码器的锂离子固态电解质定向生成**

本目录包含了本项目用于**生成全新晶体结构**的核心生成模型代码。
基于开源的 **CDVAE (Crystal Diffusion Variational Autoencoder)** 架构（https://github.com/txie-93/cdvae），我们对其进行了针对性改造，使其从一个通用的材料生成器，转变为专精于“高稳定、宽窗口”锂离子固态电解质的定向生成引擎。

---

## 项目背景与创新思路 (Background & Motivation)

### 原始 CDVAE 旨在解决什么问题？
传统的材料发现依赖于元素替换或基于已知晶格的局部调整，很难跳出已有的化学空间。CDVAE 原作提出了一种基于扩散模型 (Diffusion Model) 和图神经网络的三维生成框架，它能够在潜空间中同时学习原子的三维坐标与元素类型，从而生成自然界中不存在的全新周期性晶体结构。

### 我们的定向生成策略 (Two-Step Generation Strategy)
为了让 AI 不是“漫无目的”地生成，而是精准降落到优质电解质的化学空间，本项目设计了递进性的训练策略：

* **Step 1: 无条件生成 (Unconditional Generation)**
  * **目标：** 让模型单纯学习我们清洗后的锂离子固态电解质数据集（如硫银锗矿、石榴石、NASICON等骨架的拓扑特征），掌握原子的基本排列规律与物理距离限制。
* **Step 2: 属性优化与条件生成 (Conditional Generation for Property Optimization)**
  * **目标：** 在模型学会生成晶体的基础上，引入**形成能 (Formation Energy)** 和 **带隙值 (Band Gap)** 作为条件引导（Conditioning）。
  * **效果：** 迫使扩散过程向着“能量更低（更稳定）”且“带隙更大（绝缘防漏电）”的三维坐标空间收敛，从而实现极品固态电解质的**定向生成**。

> ** 代码说明 (Codebase Notice):**
> 为了保持仓库的精简并直接聚焦于最终的科研目标，本目录上传的源码与配置文件**主要反映 Step 2（条件生成/属性优化）阶段的最终形态**。

---

## 核心代码与配置文件清单 (Uploaded Files)

我们在原始 CDVAE 的基础上，对数据流、模型结构和评估脚本进行了深度适配。以下是本目录包含的核心文件及其功能说明：

### 1. 配置文件 (Config Files)
* `conf/data/li_electrolytes.yaml`: **【新增】** 针对无条件生成任务构建的数据集配置文件，定义了过滤规则与路径。
* `conf/data/li_prop.yaml`: **【新增】** 针对属性优化任务构建的条件生成配置文件，绑定了形成能与带隙标签。
* `conf/model/vae.yaml`: **【修改】** 调整了潜空间维度与扩散步数，以适应单胞原子数在 20-60 之间的复杂电解质网络。
* `conf/optim/default.yaml`: 优化器与学习率调度器配置。

### 2. 模型与数据处理源码 (Source Code)
* `pl_data/dataset.py`: **【修改】** 重写了数据加载逻辑，使其能够正确解析包含锂离子特征以及多重属性标签（$E_f$, Band Gap）的数据集。
* `pl_modules/model.py`: **【修改】** 调整了扩散模型前向与反向过程中的条件注入 (Condition Injection) 逻辑，使生成过程能够响应特定的属性目标。
* `scripts/evaluate.py`: **【修改】** 评估脚本适配。用于在生成后批量计算新材料的 Valid (结构有效性)、Unique (唯一性) 和 Novel (新颖性) 等关键生成指标。

### 3. 超参数记录 (Hyperparameters)
* `hparams/`: 包含了我们在无条件生成和条件生成两次关键训练中使用的 `hparams.yaml` 文件，完整记录了潜变量维度、GNN 层数及扩散调度参数，供复现参考。

---

## 生成成果去哪了？(Where are the generated materials?)

经过 CDVAE 条件生成出的高潜力原始晶体结构（数百个全新化学式的 CIF 文件），以及它们经过 CHGNet 弛豫后的三维渲染图，在主仓库的最终结果展示区
👉 **请前往根目录下的 `results/generated_cifs/` 文件夹查看！**

*(这些由 AI 创造的结构，随后被全部送入了本仓库提供的 CGCNN 与巨势相图流水线，接受了极为严苛的电化学窗口筛选。)*
