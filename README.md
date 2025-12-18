
# AgentTorch Comprehensive Deployment Guide | AgentTorch 全面部署指南

[![OS-Windows](https://img.shields.io/badge/OS-Windows%2011-blue?logo=windows)](https://www.microsoft.com/windows)
[![OS-Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2024.04-orange?logo=ubuntu)](https://ubuntu.com/)
[![OS-macOS](https://img.shields.io/badge/OS-macOS%20(M1/M2/M3)-lightgrey?logo=apple)](https://www.apple.com/macos/)
[![GPU-NVIDIA](https://img.shields.io/badge/GPU-RTX%2030%2F40%2F50-green?logo=nvidia)](https://www.nvidia.com/)
[![CUDA-Multi](https://img.shields.io/badge/CUDA-11.8%20--%2012.8%2B-green?logo=nvidia)](https://developer.nvidia.com/cuda-toolkit)
[![License-MIT](https://img.shields.io/badge/License-MIT-yellow)](https://opensource.org/licenses/MIT)

> **"Empowering social complexity research through high-performance differentiable simulation, across all platforms."**  
> **“跨平台高性能可微仿真，赋能社会复杂性研究。”**

---

### 🌟 Project Philosophy | 项目理念

**English:**
Research should be boundaryless. Whether you are on a high-performance Windows workstation, a Linux cluster, or a sleek MacBook, the power of AgentTorch should be at your fingertips. Our philosophy is **"Universal, Adaptive, and Researcher-Centric"**. We focus on solving the "last mile" of deployment, ensuring that the latest GPU architectures (from RTX 30 series to the newest Blackwell 50 series) and Apple Silicon (MPS) are fully optimized for humanities and social science simulation.

**中文：**
研究不应有边界。无论你使用的是高性能 Windows 工作站、Linux 集群，还是轻便的 MacBook，AgentTorch 的力量都应触手可及。我们的理念是 **“普适、自适应、以研究者为中心”**。我们致力于解决部署的“最后一公里”，确保从 RTX 30 系列到最新的 Blackwell 50 系列显卡，以及 Apple Silicon (MPS) 架构，都能为人文社科模拟提供最优性能支持。

---

### ❤️ Acknowledgement & Homage | 致敬官方与版权说明

This repository is a **Community-Driven Deployment Extension** for the original **AgentTorch** project. 

We would like to express our deepest gratitude and respect to the **AgentTorch Team** (Official Repository: [AgentTorch/AgentTorch](https://github.com/AgentTorch/AgentTorch)). Their groundbreaking work in building a *differentiable, GPU-accelerated agent-based modeling framework* has revolutionized how we simulate and optimize complex large-scale systems.

**本仓库是 AgentTorch 官方项目的社区化部署扩展指南。**

我们向 **AgentTorch 官方团队**（项目主页：[AgentTorch/AgentTorch](https://github.com/AgentTorch/AgentTorch)）致以最诚挚的感谢与敬意。他们在构建 *可微、GPU 加速的大群体行为建模框架* 方面所做的开创性工作，彻底改变了我们模拟与优化大规模复杂系统的方式。本指南旨在打破硬件藩篱，让更多跨学科研究者受益。

---

### 🚀 Key Features | 本指南核心亮点

*   **🎮 Full NVIDIA Spectrum**: Tailored support for **RTX 30 (Ampere)**, **RTX 40 (Ada)**, and **RTX 50 (Blackwell)** series.
    *   **全系 NVIDIA 支持**：针对 **RTX 30/40/50 系列** 显卡的精细化配置。
*   **🍎 macOS Native Optimization**: Leveraging **Apple Silicon (M1/M2/M3)** via Metal Performance Shaders (MPS).
    *   **macOS 原生优化**：充分利用 Apple Silicon 芯片的 **MPS 加速** 技术。
*   **🐍 Flexible CUDA Versions**: Support for CUDA **11.8, 12.1, 12.4, 12.6, and 12.8+**.
    *   **灵活的 CUDA 适配**：覆盖从 11.8 到最新 12.8+ 的全版本 CUDA 安装方案。
*   **📂 C-Drive Clean Deployment**: For Windows users, we prioritize non-system disk (D-Drive) environment isolation.
    *   **C 盘清爽部署**：针对 Windows 用户，优先采用非系统盘环境隔离逻辑。

---

### 📂 Navigation | 快速导航

| 操作系统 (OS) | 核心特性 | 部署指南 |
| :--- | :--- | :--- |
| **Windows 11** | RTX 30/40/50 + 非C盘部署 | [View Guide](./windows_guide.md) |
| **Ubuntu 24.04** | 物理机/WSL2/深度服务器 | [View Guide](./ubuntu_guide.md) |
| **macOS** | M1/M2/M3 (MPS 加速) | [View Guide](./mac_guide.md) |

---

### 🛠️ Quick Hardware Check | 快速硬件自查命令

**Windows (PowerShell):**
```powershell
nvidia-smi  # 检查 NVIDIA 显卡与驱动
```

**macOS (Terminal):**
```bash
sysctl -n machdep.cpu.brand_string  # 检查芯片型号 (M1/M2/M3)
```

---

### 🤝 Contributing & Support | 贡献与支持

This guide is maintained by **Baireinhold** with AI collaborative support from **Gemini** and **Claude**. We welcome feedback from scholars in Digital Humanities, Computational Social Science, and beyond.

本指南由 **Baireinhold** 维护，由 **Gemini** 与 **Claude** 提供 AI 协同支持。我们欢迎数字人文、计算社会科学及相关领域学者的反馈。

---

**Disclaimer:** *AgentTorch is a trademark/project of its respective owners. This guide is an independent community contribution and is not officially affiliated with the AgentTorch team.*
