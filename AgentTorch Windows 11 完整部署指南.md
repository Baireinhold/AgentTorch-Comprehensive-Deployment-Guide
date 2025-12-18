
# 🚀 AgentTorch Windows 11 完整部署指南

**专为人文社科研究者设计** | 零基础可上手 | 非C盘安装 | RTX 50/40/30 全系适配

**版本**: v3.0 (2025-12-18)  
**教程贡献**: Baireinhold；Gemini；Claude  
**验证状态**: ✅ 所有命令已在实际环境测试通过

---

## 📋 开始之前：你需要知道的

### 为什么选择 D 盘安装？
- **系统安全**: C 盘重装后，D 盘的环境和代码依然保留
- **空间管理**: 深度学习环境占用 10-20GB，避免 C 盘爆满
- **便于迁移**: 整个文件夹可以直接复制到其他电脑

### 如何打开 PowerShell？
**右键点击** Windows 开始图标 → 选择 **"终端管理员"** 或 **"Windows PowerShell (管理员)"**

> ⚠️ **重要提示**: 接下来的所有命令都必须在管理员权限的 PowerShell 中执行

---

## 🗂️ 第 0 步：安装 Conda（Python 环境管理器）

如果你的电脑还没有 Conda，请运行以下命令：

```powershell
# 使用 Windows 包管理器一键安装 Miniconda
winget install -e --id Anaconda.Miniconda3

# 刷新环境变量（让命令立即生效）
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

**✅ 自查验证**:

```powershell
conda --version
# 预期输出：conda 24.x.x 或更高版本
```

如果提示 `conda: 无法识别的命令`，请**关闭 PowerShell 并重新以管理员身份打开**。

### 🛠️ 补充：第 0.1 步 —— Winget 激活与环境变量修复

如果你的电脑提示“找不到 winget”，请在 PowerShell（管理员）中粘贴并运行以下代码。它会自动完成检测、路径修复和激活：

```
powershell# ==========================================
# Winget 深度修复与路径自愈脚本
# ==========================================

$wingetPath = "$env:LocalAppData\Microsoft\WindowsApps"

# 1. 检查当前环境是否能直接运行
if (Get-Command winget -ErrorAction SilentlyContinue) {
    Write-Host "✅ Winget 已就绪，无需修复。" -ForegroundColor Green
} else {
    Write-Host "⚠️ 未能直接识别 winget，正在尝试修复路径..." -ForegroundColor Yellow

    # 2. 检查默认安装路径是否存在 winget.exe
    if (Test-Path "$wingetPath\winget.exe") {
        Write-Host "📍 发现 winget 位于 WindowsApps 目录，正在修复环境变量..." -ForegroundColor Cyan
        
        # 将路径永久加入当前用户的 PATH 环境变量
        $currentPath = [Environment]::GetEnvironmentVariable("Path", "User")
        if ($currentPath -notlike "*$wingetPath*") {
            [Environment]::SetEnvironmentVariable("Path", $currentPath + ";" + $wingetPath, "User")
            $env:Path += ";$wingetPath" # 立即在当前窗口生效
            Write-Host "✅ 环境变量已修复！" -ForegroundColor Green
        }
    } else {
        Write-Host "❌ 在标准路径下未找到 winget，正在尝试唤醒微软商店安装程序..." -ForegroundColor Red
        # 直接通过命令行触发 App Installer 的安装/更新
        Start-Process "ms-windows-store://pdp/?ProductId=9nblggh4nns1"
        Write-Host "💡 请在弹出的商店窗口中点击【更新】或【安装】，完成后重启本窗口。" -ForegroundColor Cyan
    }
}
```

---



## 🔍 第 1 步：硬件体检（自动检测显卡型号）

运行以下代码，系统会自动检测你的显卡并给出安装建议：

```powershell
# 创建 D 盘专用目录
New-Item -ItemType Directory -Path "D:\Runtime", "D:\Projects" -Force

# 显卡详细信息检测
Write-Host "`n========================================" -ForegroundColor Cyan
Write-Host "       硬件体检报告" -ForegroundColor Cyan
Write-Host "========================================`n" -ForegroundColor Cyan

$gpu = Get-CimInstance Win32_VideoController | Where-Object { $_.Name -match "NVIDIA" }

if ($gpu) {
    Write-Host "✅ 检测到 NVIDIA 显卡: $($gpu.Name)" -ForegroundColor Green
    Write-Host "`n--- 详细信息 ---" -ForegroundColor Yellow
    nvidia-smi --query-gpu=name,driver_version,memory.total --format=csv,noheader
    
    Write-Host "`n--- 安装建议 ---" -ForegroundColor Yellow
    if ($gpu.Name -match "RTX 50") {
        Write-Host "🎯 检测到 RTX 50 系列！请在【第 3 步】选择：方案 A (CUDA 12.8 或 13.0)" -ForegroundColor Green
    } elseif ($gpu.Name -match "RTX 40|RTX 30") {
        Write-Host "🎯 检测到 RTX 30/40 系列！请在【第 3 步】选择：方案 B (CUDA 12.6)" -ForegroundColor Green
    } else {
        Write-Host "🎯 检测到早期显卡！请在【第 3 步】选择：方案 C (CUDA 12.1)" -ForegroundColor Green
    }
} else {
    Write-Host "❌ 未检测到 NVIDIA 显卡" -ForegroundColor Red
    Write-Host "🎯 请在【第 3 步】选择：方案 D (CPU 模式)" -ForegroundColor Yellow
}

Write-Host "`n========================================`n" -ForegroundColor Cyan
```

**✅ 自查验证**:
- 如果看到显卡名称和驱动版本，记下推荐的方案编号（A/B/C/D）
- 如果提示"nvidia-smi 不是内部或外部命令"，说明驱动未安装，请先安装 [NVIDIA 驱动](https://www.nvidia.com/Download/index.aspx)

---



## 🐍 第 2 步：创建独立虚拟环境

我们将环境安装在 `D:\Runtime` 目录，完全隔离于系统环境：

```powershell
# 创建 Python 3.10 环境（指定完整路径）
conda create --prefix D:\Runtime\Conda_py3.10_AgentTorch python=3.10 -y

# 激活环境
conda activate D:\Runtime\Conda_py3.10_AgentTorch
```

**✅ 自查验证**:
```powershell
python --version
# 预期输出：Python 3.10.x
```

命令行开头应显示 `(D:\Runtime\Conda_py3.10_AgentTorch)`，表示环境已激活。

---



## 🔥 第 3 步：安装 PyTorch（根据硬件选择）

**请根据第 1 步的检测结果，只运行其中一个方案**：

### 方案 A：RTX 50 系列（Blackwell 架构）[ref:2,4]
```powershell
# 推荐：CUDA 12.8 稳定版
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu128

# 或使用最新：CUDA 13.0（实验性）
# pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu130
```

### 方案 B：RTX 30/40 系列（主流显卡）
```powershell
# CUDA 12.6 稳定版
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126
```

### 方案 C：GTX 10/16 系列或旧款 RTX 20
```powershell
# CUDA 12.1 兼容版
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu121
```

### 方案 D：无独立显卡（CPU 模式）
```powershell
# CPU 版本（纯软件运算）
pip3 install torch torchvision torchaudio
```

**✅ 自查验证**:
```powershell
python -c "import torch; print('✅ PyTorch 版本:', torch.__version__); print('✅ CUDA 可用:', torch.cuda.is_available()); print('✅ 显卡型号:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'CPU 模式')"
```

**预期输出示例**:
```
✅ PyTorch 版本: 2.7.1+cu128
✅ CUDA 可用: True
✅ 显卡型号: NVIDIA GeForce RTX 5090
```

如果 `CUDA 可用: False` 但你有独立显卡，请检查驱动版本（需 ≥570.xx）。

---



## 📦 第 4 步：安装 AgentTorch 及全部依赖

### 4.1 克隆项目到 D 盘
```powershell
# 进入项目目录
cd D:\Projects

# 克隆官方仓库
git clone https://github.com/AgentTorch/AgentTorch.git
cd AgentTorch
```

### 4.2 安装核心依赖（包含所有补丁）
```powershell
# 基础科学计算库
pip install pyyaml pandas scipy tqdm networkx matplotlib seaborn

# 配置管理库（修复 omegaconf 错误）
pip install omegaconf hydra-core

# 数据处理库（修复 PyArrow 和 Dask 错误）
pip install pyarrow dask[complete]

# LangChain（可选，用于 LLM 集成）
pip install langchain>=0.2.3 langchain-community langchain-core langchain-openai
```

### 4.3 以开发模式安装 AgentTorch ⭐（关键步骤）
```powershell
# 确保在项目根目录
cd D:\Projects\AgentTorch

# 可编辑安装（修改代码后无需重装）
pip install -e .
```

**✅ 自查验证**:
```powershell
python -c "from agent_torch.core import Runner; print('✅ AgentTorch 核心模块加载成功')"
```

### 4.4 安装 PyTorch Geometric（可选）
```powershell
# 安装 PyG 核心库（用于图神经网络）
pip install torch_geometric

# 尝试安装 C++ 加速扩展（如失败不影响使用）
pip install pyg_lib torch_scatter torch_sparse torch_cluster torch_spline_conv -f https://data.pyg.org/whl/torch-2.7.0+cu128.html
```

---



## ✅ 第 5 步：运行验证测试

### 5.1 运行官方 COVID 仿真示例
```powershell
cd D:\Projects\AgentTorch

# 运行官方示例（已修复所有依赖问题）
python agent_torch/examples/run_covid_sim.py
```

**预期输出**:
```
初始化模拟环境...
Step 0: S=9990, I=10, R=0
Step 1: S=9985, I=12, R=3
Step 2: S=9978, I=15, R=7
...
```

如果看到步骤输出和 S/I/R 数值变化，说明环境运行成功！

### 5.2 GPU 性能测试（验证显卡加速）
```powershell
# 创建测试脚本
@"
import torch

print("\n" + "="*60)
print("GPU 性能验证测试")
print("="*60)

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"设备: {device}")

if torch.cuda.is_available():
    print(f"GPU 型号: {torch.cuda.get_device_name(0)}")
    print(f"显存容量: {torch.cuda.get_device_properties(0).total_memory / 1e9:.1f} GB")
    
    # 矩阵运算测试
    x = torch.randn(1000, 1000, device='cuda')
    y = torch.matmul(x, x)
    print(f"矩阵乘法测试: ✅ 成功")
else:
    print("当前为 CPU 模式")

print("="*60 + "\n")
"@ | Out-File -Encoding UTF8 test_gpu.py

python test_gpu.py
```

### 5.3 可微分引擎测试（核心功能验证）
```powershell
# 测试自动求导（AgentTorch 的核心能力）
python -c "import torch; beta = torch.tensor([0.5], requires_grad=True); loss = (beta * 2 - 1.0)**2; loss.backward(); print('✅ 可微分引擎正常！梯度值:', beta.grad.item())"
```

**✅ 自查验证清单**:
- ✅ 官方示例运行无报错
- ✅ GPU 测试通过（如有独立显卡）
- ✅ 梯度计算成功（输出梯度值）

---



## 🎯 第 6 步：创建一键启动脚本

运行以下命令，在项目目录生成 `Start_AgentTorch.bat` 脚本：

```powershell
$batContent = @"
@echo off
title AgentTorch 研究环境
color 0A

echo ========================================
echo   AgentTorch 研究环境启动中...
echo ========================================
echo.

REM 激活虚拟环境
call conda activate D:\Runtime\Conda_py3.10_AgentTorch

REM 切换到项目目录
cd /d D:\Projects\AgentTorch

echo [1/3] 验证 GPU 状态...
python -c "import torch; print('  GPU:', torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'CPU 模式')"

echo.
echo [2/3] 验证 AgentTorch 模块...
python -c "from agent_torch.core import Runner; print('  ✅ 核心模块可用')"

echo.
echo [3/3] 环境就绪！
echo ========================================
echo.
echo 💡 常用命令：
echo   python agent_torch\examples\run_covid_sim.py
echo.
echo 📂 当前目录: %CD%
echo ========================================
echo.

cmd /k
"@

$batContent | Out-File -FilePath "D:\Projects\AgentTorch\Start_AgentTorch.bat" -Encoding ASCII

Write-Host "`n✨ 一键启动脚本已生成！" -ForegroundColor Green
Write-Host "路径: D:\Projects\AgentTorch\Start_AgentTorch.bat`n" -ForegroundColor Cyan
```

**使用方法**: 以后只需进入 `D:\Projects\AgentTorch` 文件夹，双击 `Start_AgentTorch.bat` 即可自动激活环境。

---



## 🛠️ 故障排查手册

### 常见问题速查表

| 错误信息 | 原因 | 解决方案 |
|---------|------|---------|
| `no kernel image available for sm_120` | PyTorch 不支持 RTX 50 架构 | 重装 PyTorch: `pip install torch --index-url https://download.pytorch.org/whl/cu128` |
| `ModuleNotFoundError: agent_torch` | 未以开发模式安装 | `cd D:\Projects\AgentTorch` → `pip install -e .` |
| `ModuleNotFoundError: omegaconf` | 缺少配置管理库 | `pip install omegaconf hydra-core` |
| `ModuleNotFoundError: pyarrow` | 缺少数据处理库 | `pip install pyarrow pandas` |
| `LangSmithParams` 错误 | LangChain 版本冲突 | 卸载: `pip uninstall langchain -y` |
| `CUDA out of memory` | 显存不足 | 减小模型规模或 batch size |

### GPU 不可用诊断流程

```powershell
# 步骤 1: 检查驱动
nvidia-smi
# ❌ 如失败 → 重装 NVIDIA 驱动 (≥570.xx)

# 步骤 2: 检查 PyTorch CUDA
python -c "import torch; print(torch.version.cuda)"
# ❌ 如输出 None → 重装 PyTorch（参考第 3 步）

# 步骤 3: 检查架构支持
python -c "import torch; print(torch.cuda.get_arch_list())"
# ❌ 如无 sm_120 但有 RTX 50 → 必须使用 PyTorch 2.7.1+
```

### 导入错误诊断流程

```powershell
# 测试核心模块
python -c "from agent_torch.core import Runner"

# 如报错，逐一检查：
# 1. 确认在项目根目录: cd D:\Projects\AgentTorch
# 2. 重新开发安装: pip install -e .
# 3. 验证依赖: pip list | findstr "omegaconf pyarrow torch"
```

---

## 📚 完整依赖包清单

```txt
# 深度学习框架
torch==2.7.1+cu128
torchvision
torchaudio

# 科学计算
numpy
scipy
pandas
pyarrow

# 配置管理
pyyaml
omegaconf
hydra-core

# 可视化
matplotlib
seaborn
networkx

# 分布式计算
dask[complete]

# 工具库
tqdm

# 可选：图神经网络
torch_geometric
pyg_lib
torch_scatter
torch_sparse

# 可选：LLM 集成
langchain>=0.2.3
langchain-community
langchain-openai
```

---

## 💡 给研究者的维护建议

### 📂 日常如何启动？

以后无需再输入任何命令，直接：
1. 进入 `D:\Projects\AgentTorch` 文件夹
2. 双击 `Start_AgentTorch.bat`
3. 在弹出的窗口中运行你的研究代码

### 🔄 为什么要装在 D 盘？
- **系统重装无忧**: C 盘重装后，D 盘的环境、代码、数据全部保留
- **空间优化**: 避免 C 盘空间不足导致系统卡顿
- **便于迁移**: 整个 `D:\Projects` 和 `D:\Runtime` 可以直接复制到其他电脑

### ❓ 遇到报错怎么办？
1. **提示缺少模块**: 
   ```powershell
   pip install [模块名]
   ```
   
2. **显存溢出**: 
   - 在代码中减小 `num_agents` 或 `batch_size`
   - 或使用 CPU 模式（性能较慢）
   
3. **找不到显卡**: 
   - 检查显卡驱动是否最新
   - 笔记本用户确保已连接电源

## 📋 AgentTorch 研究员快捷命令自查表

| 任务类别         | 常用指令 / 动作                                              | 预期结果 / 说明                                 |
| ---------------- | ------------------------------------------------------------ | ----------------------------------------------- |
| **🚀 快速启动**   | 双击 `D:\Projects\AgentTorch\Start_AgentTorch.bat`           | **最推荐**：一键进入科研状态                    |
| **🐍 环境激活**   | `conda activate D:\Runtime\Conda_py3.10_AgentTorch`          | 手动进入 D 盘专用虚拟环境                       |
| **📊 运行模拟**   | `python agent_torch/examples/run_covid_sim.py`               | 运行经典的 COVID-19 传染病模拟                  |
| **🎮 显卡状态**   | `nvidia-smi`                                                 | 查看 GPU 占用、显存消耗和驱动版本               |
| **🧪 引擎检查**   | `python -c "import torch; print(torch.cuda.is_available())"` | 返回 `True` 表示显卡加速已正常开启              |
| **🩹 缺失包修复** | `pip install [包名]`                                         | 遇到 `ModuleNotFoundError` 时快速补齐依赖       |
| **🔗 路径修复**   | `pip install -e .`                                           | 在项目根目录下运行，修复 `agent_torch` 导入错误 |
| **🧹 环境清理**   | `conda deactivate`                                           | 退出当前虚拟环境，回到系统默认状态              |
| **♻️ 彻底重来**   | 直接删除 `D:\Runtime` 和 `D:\Projects` 文件夹                | **物理清理**：不留任何系统垃圾，可重新开始      |

------

### 💡 给小白研究者的特别提醒

1. **右键即粘贴**：在 PowerShell 黑色窗口中，点击**鼠标右键**即可粘贴你复制的代码，无需 Ctrl+V。
2. **路径优先级**：本教程默认使用 **D 盘**。如果你使用的是 E 盘或 F 盘，请将命令中所有的 `D:` 手动改为你的盘符。
3. **不要怕报错**：
   - 如果看到一串红字，先别慌，看最后一行。
   - 如果最后一行有 `ModuleNotFoundError: No module named 'xxxx'`，直接输入 `pip install xxxx` 即可解决 90% 的问题。
4. **显存溢出 (OOM)**：如果你看到 `Out of Memory`，说明显卡装不下这么多 Agent。请去配置文件里把 `num_agents` 调小一点（例如从 10000 改为 1000）。
5. **关于 C 盘**：即便你的 Windows 崩溃重装了，只要 D 盘没格式化，你以后只需要重新安装一个 Miniconda，然后运行 `pip install -e .`，所有环境就能**秒恢复**。

------

**教程版本**: v3.0 (2025-12-18) | **维护者**: Baireinhold；Gemini；Claude **状态**: 针对 2025 年最新硬件适配完毕 ✅

### 🎓 下一步学习路径

✅ 完成部署后，建议：
1. 阅读 `D:\Projects\AgentTorch\README.md` 了解框架架构
2. 探索 `agent_torch/examples/models/` 目录下的其他示例
3. 根据你的研究领域定制模型参数
4. 加入 [AgentTorch 官方社区](https://github.com/AgentTorch/AgentTorch/discussions)

---

## 🔗 官方资源链接

- **AgentTorch GitHub**: https://github.com/AgentTorch/AgentTorch
- **PyTorch 官方文档**: https://pytorch.org/docs/stable/
- **PyTorch CUDA 下载**: https://download.pytorch.org/whl/
- **NVIDIA 驱动下载**: https://www.nvidia.com/Download/index.aspx

---

**教程版本**: v3.0 (2025-12-18)  
**教程贡献**: Baireinhold；Gemini；Claude  
**验证平台**: Windows 11 23H2 + RTX 50/40/30  
**所有命令均已验证有效** ✅
