# Project Results (项目成果展示)

本目录归档了本项目在模型训练和三维晶体生成阶段的核心产出数据与可视化图表。

---

## 1. CGCNN 模型训练成果 (Property Prediction Results)

在探索固态电解质属性预测模型的过程中，我们通过架构优化与超参数调优，成功获得了一个能够有效抑制过拟合且精度优异的预测模型。
核心成果保存在 `epoch200_upgrade_batch/` 文件夹中，包含最佳权重 `model_best.pth.tar`、训练日志 `train_log.txt` 以及测试集预测结果 `test_results.csv`。

**最佳模型超参数组合与性能：**
* **Epoch:** 200
* **Batch Size:** 64
* **学习率动态调度:** Epoch 0-100: `0.005` | 100-150: `0.0005` | 150-200: `0.00005`
* **权重衰减系数 (L2 Regularization):** 10^-4
* **网络正则化:** 引入 Dropout 层 (p=0.2，位于池化层后、全连接层前)
* **最终性能:** 最佳模型精度 **MAE = 0.181V** (于 Epoch 158 取得)，过拟合程度控制在 30%，训练集与验证集 MAE 差距缩小至 0.055V。

*(注：训练过程的 Loss 衰减曲线与 MAE 性能图已上传至当前目录，以供直观对比参考。)*

---

## 2. CDVAE 晶体生成与弛豫结构 (Generated & Relaxed Materials)

本模块展示了 AI 扩散模型在三维空间中创造的新型固态电解质晶体候选物。为了便于审阅，所有 CIF 结构文件已打包为压缩包，并附带了部分极品材料的 VESTA 三维渲染图。

生成工作流分为三个递进的阶段：
* 📦 **`Generated_cifs.zip` (无条件生成):** 包含 100 个由模型初步学习锂离子电解质空间拓扑后，自由探索生成的原始材料结构。
* 📦 **`Generated_Electrolytes_prop.zip` (属性条件生成):** 包含 352 个在引入特定属性条件引导后，定向生成的高潜力电解质结构（未经力场优化，可能存在局部原子过近）。
* 📦 **`Relaxed_Generated_Electrolytes_prop.zip` (极速结构弛豫):** 上述 352 个条件生成材料在经过机器学习力场初步优化后的形态，旨在消除晶格应力与原子碰撞。

*(每个分类下均精选了 10 张左右 VESTA 结构渲染图，直观展示 AI 生成的多阴离子骨架与锂离子传导通道。)*
---
---

## 📸 晶体结构可视化 (Structure Visualization)

本项目生成的候选材料已通过 VESTA 进行了高精度渲染。展示分为两个阶段：初始生成的探索性结构（Generated）与经过机器学习力场弛豫后的稳态结构（Relaxed）。

### 1. 核心精选展示 (Top Highlights)
我们从 **23 个** 精选渲染图中挑选了 3 个最具代表性的结构。这些材料在保持了高对称性的同时，展现了清晰的锂离子三维扩散通道。

| 结构状态 (Status) | 典型渲染图 (Representative Image) | 化学式与亮点 (Formula & Features) |
| :--- | :---: | :--- |
| **弛豫后稳态** | ![Example 1](./Relaxed_Generated_Electrolytes_prop_img/YOUR_IMAGE_NAME_1.png) | **Li-Zr-O-F 体系**：经过 CHGNet 优化，结构展现出极佳的热力学稳定性与拓扑对称性。 |
| **初始生成态** | ![Example 2](./Generated_cifs_img/YOUR_IMAGE_NAME_2.png) | **Li-P-S-Cl 体系**：AI 成功模拟了经典的硫化物/卤化物复合骨架，离子通道宽敞。 |
| **对比优化态** | ![Example 3](./Relaxed_Generated_Electrolytes_prop_img/YOUR_IMAGE_NAME_3.png) | **Li-Sb-S 体系**：通过条件扩散模型定向生成，具备优异的电化学窗口潜力。 |

> [!TIP]
> **如何查看更多？**
> * 📂 **初始生成结构库**：查看 [`./Generated_cifs_img/`](./Generated_cifs_img/) 文件夹（内含 13 张精选图片）。
> * 📂 **优化后性能结构库**：查看 [`./Relaxed_Generated_Electrolytes_prop_img/`](./Relaxed_Generated_Electrolytes_prop_img/) 文件夹（内含 10 张精选图片）。
> * 🔬 **深度交互**：您可以下载本目录下的 `.cif` 压缩包，并使用 **VESTA** 或 **CrystalMaker** 软件手动打开，以进行任意角度的旋转、配位多面体分析及孔道尺寸测量。

---


