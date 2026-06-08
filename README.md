# 🔋 Li-Electrolyte-Generation
**AI-Driven High-Throughput Generation and Screening Pipeline for Solid-State Lithium Battery Electrolytes** *(AI 驱动的锂离子固态电解质高通量生成与系统性评估流水线)*

![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)
![PyTorch](https://img.shields.io/badge/PyTorch-1.12.1-EE4C2C.svg)
![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)

---

## 项目简介 (Introduction)
发展高离子电导率、宽电化学窗口（ECW）且热力学稳定的新型无机固态电解质（SSE），是突破下一代高能量密度固态锂电池瓶颈的关键技术。然而，传统的“试错式”实验合成与盲目的计算筛选面临着巨大的未知化学搜索空间。

本项目搭建了一套**“生成式 AI 创造晶体 $\rightarrow$ 机器学习力场极速弛豫 $\rightarrow$ 图神经网络物性精准定量”**的端到端开源发现工作流。通过深度结合晶体扩散模型（CDVAE）的逆向材料发现能力与晶体图网络（CGCNN）的电化学窗口高精度预测，实现了由传统数据筛选向“定向设计高性能电池材料”的跨越。

---

## 核心全流程工作流 (Scientific Workflow)

整个项目的核心推进路线由四大高度紧密衔接的板块构成：

```text
  [ 1. 物理约束数据集构建 ] 
             │  (清洗并提炼高质致密数据)
             ▼
  [ 2. CGCNN 电化学窗口建模 ] ──► 获得最优超参数预测器 (MAE=0.181V)
             │
             ▼
  [ 3. CDVAE 固态电解质定向生成 ] ──► 两步走策略采出全新条件候选物
             │
             ▼
  [ 4. CHGNet 机器学习力场弛豫 ] ──► 消除晶格应力，产出最终稳态物理结构
```

1. **物理约束数据集构建 (Data Construction):** 依托 Materials Project (MP) 数据库，针对电化学窗口任务清洗出 **15,925 条** 致密去重数据集；针对电解质生成任务应用带隙、单胞规模及全有序等严格无机物理过滤器，提炼出 **5,503 条** 包含典型 SSE 骨架的高质量标准晶体库。
2. **超参数调优物性评估 (Property Prediction):** 基于 CGCNN 架构，针对复杂的电化学窗口（ECW）预测任务进行深度的超参数扩容与正则化调优（128/4/256/2 架构），成功攻克过拟合与保守预测瓶颈，使模型预测精度大幅跃升至 **MAE = 0.181 V**。
3. **两步走定向材料发现 (Generative Discovery):** 引入 CDVAE 晶体扩散模型。首先通过**无条件生成**学习无机锂离子通道的空间拓扑分布；随后通过**条件生成**注入形成能与带隙值等约束条件，引导模型向高潜力区域定向探索，创造出全新骨架材料。
4. **极速几何优化 (Structure Relaxation):** 采用通用机器学习力场 **CHGNet** 代替昂贵且耗时的传统第一性原理 VASP 计算，在极短时间内将 AI 生成的原始“粗胚结构”进行全弛豫几何优化，消除原子碰撞，使其回归物理真实稳态。

---

## 仓库目录导航 (Repository Structure)

本项目结构清晰，各子目录均配备了详尽的学术及运行说明文档（`README.md`），请点击对应链接查阅：

* 📂 **[`dataset/`](./dataset)** : 数据集存放区。包含清洗后的唯一化电化学窗口数据集描述文件以及 CDVAE 训练所需的固态电解质骨架配置。
* 📂 **[`cgcnn/`](./cgcnn)** : 属性预测模块。记录了 CGCNN 针对电化学窗口任务的大模型架构扩容、Batch Size 调优（Batch=64）及引入 Dropout（p=0.2）正则化的完整调优轨迹。
* 📂 **[`cdvae/`](./cdvae)** : 条件生成模块。包含条件生成任务的代码与配置文件清单，详细介绍了从无条件结构探索走向带标签属性定向优化的设计思路。
* 📂 **[`results/`](./results)** : 最终成果归档。包含电化学窗口预测最优权重模型（`model_best.pth.tar`）、无条件与条件生成的 CIF 结构压缩包、经 CHGNet 优化后的稳态晶体库，以及 VESTA 渲染图。
* 📄 **`requirements_cgcnn.txt`** & **`requirements_cdvae.txt`** : 专为双环境划分提供的依赖包清单，用于一键还原开发环境。
* 📄 **`.gitignore`** : 精准配置的高级排除文件，在拉黑缓存与数十万小文件的同时，为核心成果模型开辟了 VIP 上传白名单。

---

## 环境配置与快速复现 (Installation)

推荐在搭载 NVIDIA GPU 的 Linux 服务器环境下，使用 Conda 构建隔离的双虚拟环境：

### 1. 配置 CGCNN 性质预测环境
```bash
conda create -n cgcnn_env python=3.8 -y
conda activate cgcnn_env
# 安装核心依赖（请根据实际 CUDA 版本对齐 PyTorch 安装指令）
pip install -r requirements_cgcnn.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 2. 配置 CDVAE 扩散生成环境
```bash
conda create -n cdvae_env python=3.8 -y
conda activate cdvae_env
# 安装图几何扩散所需的高级三维点云依赖
pip install -r requirements_cdvae.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

---

## 核心成果摘要 (Key Achievements)

*  **极高精度的窗口质检器:** 调优后的大模型配置（Batch=64, Dropout=0.2）在独立的测试集上斩获了 **MAE = 0.181 V** 的顶尖收敛精度，泛化性能极为强悍。
*  **AI 驱动的全新物相发现:** 成功完成了无条件探索（100个新材料）与条件定向属性优化（352个大带隙低能量新材料）的三维晶体发现。
*  **物理稳态闭环:** 借助机器学习力场，在无传统 VASP 算力损耗的前提下，完成了对全新未知晶体几何优化的完全释放，为固态电池材料发现提供了丰富的、具备物理稳态的候选结构库。

---

## 🤝 致谢 (Acknowledgments)
底层算法架构参考并致谢开源项目 [CGCNN](https://github.com/txie-93/cgcnn) 与 [CDVAE](https://github.com/txie-93/cdvae)。
