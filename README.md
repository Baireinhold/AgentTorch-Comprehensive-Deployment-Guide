
# AgentTorch: Ultimate Cross-Platform Deployment Guide 
# AgentTorch 全平台原生加速部署指南

[![OS-Windows](https://img.shields.io/badge/OS-Windows%2011-blue?logo=windows)](./windows_guide.md)
[![OS-Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2022.04%2F24.04-orange?logo=ubuntu)](./ubuntu_guide.md)
[![OS-macOS](https://img.shields.io/badge/OS-macOS%20(M1--M4)-lightgrey?logo=apple)](./mac_guide.md)
[![GPU-NVIDIA](https://img.shields.io/badge/GPU-RTX%2030%2F40%2F50-green?logo=nvidia)](https://www.nvidia.com/)
[![GPU-AMD](https://img.shields.io/badge/GPU-ROCm%20Native-red?logo=amd)](https://www.amd.com/en/developer/rocm.html)
[![License-MIT](https://img.shields.io/badge/License-MIT-yellow)](https://opensource.org/licenses/MIT)

> **"Native Performance for Every Researcher: NVIDIA CUDA, AMD ROCm, and Apple Silicon."**  
> **“为每一位研究者提供原生性能：适配 NVIDIA CUDA、AMD ROCm 与 Apple Silicon。”**

---

### 🌟 Project Philosophy | 项目理念

**English:**
The complexity of human society deserves the most powerful computational tools, regardless of your hardware choice. Our mission is to bridge the gap between cutting-edge differentiable simulation and diverse hardware environments. By leveraging **Native Acceleration** (CUDA, ROCm, MPS), we ensure that social scientists can focus on insights rather than environment debugging. We advocate for a **"Universal, High-Performance, and Clean"** research workspace.

**中文：**
人类社会的复杂性值得最强大的计算工具来模拟，无论你选择何种硬件。我们的使命是在尖端可微仿真技术与多样化的硬件环境之间搭建桥梁。通过利用 **原生加速技术**（CUDA, ROCm, MPS），我们确保社会科学家能够专注于洞察发现而非环境调试。我们倡导 **“普适、高性能、纯净”** 的科研工作空间。

---

### ❤️ Acknowledgement & Homage | 致敬官方与版权说明

This repository is a **Community-Driven Deployment & Application Extension** for the original **AgentTorch** project. 

We would like to express our deepest gratitude to the **AgentTorch Team** (Official Repository: [AgentTorch/AgentTorch](https://github.com/AgentTorch/AgentTorch)). Their visionary work in building a *differentiable, GPU-accelerated agent-based modeling framework* provides the core engine for this guide. We are committed to extending its accessibility to the broader research community.

**本仓库是 AgentTorch 官方项目的社区部署与应用扩展指南。**

我们向 **AgentTorch 官方团队**（项目主页：[AgentTorch/AgentTorch](https://github.com/AgentTorch/AgentTorch)）致以崇高的敬意。他们在构建 *可微、GPU 加速的大群体行为建模框架* 方面的远见卓识，为本指南提供了核心引擎。我们致力于降低其门槛，让这一卓越框架惠及更广泛的研究群体。

---

### 🚀 Key Features | 本指南核心亮点

*   **⚡ NVIDIA Blackwell Ready**: Full optimization for **RTX 30/40/50** series with CUDA 12.x support.
    *   **深度适配 NVIDIA 全系**：完美支持 **RTX 30/40/50** 系列显卡及最新 CUDA 环境。
*   **🔴 Native AMD ROCm**: Support for **AMD GPUs** on both Windows and Ubuntu (22.04/24.04) using native ROCm kernels.
    *   **原生 AMD 加速**：支持 Windows 与 Ubuntu 双系统的 **AMD ROCm 原生部署**，彻底释放显卡算力。
*   **🍎 Apple Silicon M1-M4**: Optimized Metal Performance Shaders (MPS) support for the entire Mac lineup.
    *   **苹果芯片全家桶**：原生支持从 **M1 到最新的 M4** 芯片，利用 MPS 实现高效仿真。
*   **📂 Scholar-Centric Deployment**: Enforced "C-Drive Clean" logic for Windows and isolated environments for Linux/Mac.
    *   **以学者为中心**：坚持 Windows “C 盘零污染”部署，为各系统提供隔离、纯净的运行环境。

---

### 📂 Navigation | 快速导航

| 平台 (Platform) | 硬件架构 (Hardware) | 核心技术 (Backend) | 指南链接 (Link) |
| :--- | :--- | :--- | :--- |
| **Windows 11** | NVIDIA RTX 30/40/50 | CUDA 11.8 - 12.8+ | [View](./windows_nvidia.md) |
| **Windows 11** | AMD Radeon Series | **ROCm for Windows** | [View](./windows_amd.md) |
| **macOS** | Apple M1 / M2 / M3 / M4 | MPS (Metal) | [View](./mac_guide.md) |
| **Ubuntu** | NVIDIA / AMD ROCm | CUDA / ROCm Native | [View](./ubuntu_guide.md) |

---

### 🛠️ Quick Diagnostics | 快速环境自查

运行以下命令确认你的硬件与驱动状态：

*   **NVIDIA (Win/Linux):** `nvidia-smi`
*   **AMD (Win/Linux):** `rocm-smi` 或 `rocminfo`
*   **Apple Silicon:** `sysctl -n machdep.cpu.brand_string`

---

### 🤝 Future Roadmap | 后续计划

We are not just providing scripts; we are building an ecosystem for computational social science.
我们不仅提供脚本，更在构建计算社会科学的生态系统。

*   **Simulation Zoo**: Open-source collection of pre-built behavior simulation instances.
    *   **仿真模型库**：持续开源各类预构建的社会行为仿真实例。
*   **Performance Benchmarks**: Detailed metrics across different hardware architectures.
    *   **性能基准测试**：提供不同硬件架构下的详细运行效率对比。

---

**Disclaimer:** *AgentTorch is a trademark/project of its respective owners. This guide is an independent community contribution and is not officially affiliated with the AgentTorch team.*

**免责声明：** *AgentTorch 是其原作者的商标/项目。本指南为独立的社区贡献，不代表 AgentTorch 官方团队。*
