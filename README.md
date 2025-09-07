# NVIDIA NeMo 智能待办管家（File-Linked 增强版）

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![NVIDIA NeMo](https://img.shields.io/badge/Powered%20by-NVIDIA%20NeMo-orange.svg)](https://developer.nvidia.com/nemo)

## 一、项目简介

基于 **NVIDIA NeMo Agent Toolkit** 开发的智能待办管理系统，核心解决个人/团队任务管理中“任务与关联资料脱节”的痛点，支持**任务分类、优先级管理、本地文件深度关联、过期提醒**等功能，并提供可视化前端界面，实现“任务-文件-统计”全流程闭环管理。

### 核心亮点

1. **NVIDIA 技术深度集成**：基于 NeMo Agent Toolkit 的 React 工作流与工具注册机制，确保功能扩展性与兼容性；
2. **文件关联增强**：添加待办时自动验证本地文件有效性，存储绝对路径避免失效，支持文件关联的全生命周期管理；
3. **前后端可视化交互**：前端基于 React/Vite 构建，提供直观的任务操作界面，后端通过 NeMo Agent 暴露 API 实现高效交互；
4. **智能统计与提醒**：集成 LLM 生成自然语言任务总结，自动提醒过期任务，降低管理成本。

## 二、项目结构

项目根目录为 `hackathon_aiqtoolkit`，关键目录与文件如下（仅展示核心路径）：

```
hackathon_aiqtoolkit/          # 项目根目录
├─ configs/                    # 配置文件目录
│  └─ hackathon_config.yml     # 核心配置（工具、LLM、端口等）
├─ external/                   # 前端项目目录
│  └─ aiqtoolkit-opensource-ui # 可视化前端（React+Vite）
└─ NeMo-Agent-Toolkit/         # 后端核心（NVIDIA NeMo Agent）
   ├─ src/
   │  └─ nat/
   │     └─ tool/              # 自定义工具实现
   │        ├─ todo_manager.py # 待办管理工具（含文件关联）
   │        └─ register.py     # 工具注册（接入NeMo生态）
   └─ .venv/                   # Python虚拟环境（部署时创建）
```

## 三、环境准备

### 3.1 后端环境（NeMo Agent）

- **Python 版本**：3.9 ~ 3.11（需匹配 NeMo Agent Toolkit 要求）
- **包管理器**：`uv`（高性能 Python 包管理器，推荐优先使用）
- **依赖来源**：项目内置依赖清单（通过`uv sync`自动安装）

### 3.2 前端环境（可视化界面）

- **Node.js 版本**：16.x ~ 20.x（LTS 版本最佳）
- **包管理器**：`npm`（Node.js 自带）或`yarn`

## 四、快速部署

部署分为「后端服务启动」和「前端界面启动」两步，需分别在对应目录执行命令。

### 4.1 步骤 1：部署后端（NeMo Agent 服务）

#### 1.1 进入后端目录

打开终端，切换到`NeMo-Agent-Toolkit`目录（需替换为你的实际项目路径）：

```bash
# Windows示例（假设项目在D盘）
cd D:\hackathon_aiqtoolkit\NeMo-Agent-Toolkit

# Linux/macOS示例（假设项目在用户目录）
cd ~/hackathon_aiqtoolkit/NeMo-Agent-Toolkit
```

#### 1.2 创建并激活虚拟环境

使用`uv`创建名为`.venv`的虚拟环境，避免依赖冲突：

```bash
# 1. 创建虚拟环境
uv venv

# 2. 激活虚拟环境
# Windows系统
.venv\Scripts\activate
# Linux/macOS系统
source .venv/bin/activate
```

激活成功后，终端前缀会显示`(.venv)`。

#### 1.3 安装后端依赖

通过`uv sync`安装所有依赖（含 NeMo Agent 核心包、待办工具依赖等）：

```bash
uv sync --all-groups --all-extras
```

- `--all-groups`：安装所有依赖组（工具、LLM、服务等）
- `--all-extras`：安装包的额外功能（确保文件关联、统计等功能正常）

#### 1.4 启动后端服务

加载配置文件并启动 API 服务，绑定`0.0.0.0:8001`（支持局域网访问）：

```bash
nat serve --config_file ../configs/hackathon_config.yml --host 0.0.0.0 --port 8001
```

**后端启动成功标志**：
终端显示类似以下日志，说明服务已就绪：

```
2024-XX-XX XX:XX:XX INFO     Starting NeMo Agent server on 0.0.0.0:8001
2024-XX-XX XX:XX:XX INFO     Loaded tools: ['internet_search', 'current_datetime', 'todo_manager']
2024-XX-XX XX:XX:XX INFO     Server ready to handle requests
```

### 4.2 步骤 2：部署前端（可视化界面）

#### 2.1 进入前端目录

打开**新终端**（避免与后端终端冲突），切换到前端目录：

```bash
# Windows示例
cd D:\hackathon_aiqtoolkit\external\aiqtoolkit-opensource-ui

# Linux/macOS示例
cd ~/hackathon_aiqtoolkit/external/aiqtoolkit-opensource-ui
```

#### 2.2 安装前端依赖（首次启动需执行）

```bash
npm install
```

- 若出现依赖冲突，可尝试强制安装：`npm install --force`
- 若速度较慢，可切换 npm 镜像：`npm config set registry https://registry.npm.taobao.org`

#### 2.3 启动前端服务

```bash
npm run dev
```

**前端启动成功标志**：
终端显示类似以下日志，可通过浏览器访问：

```
VITE v4.5.0 ready in 350 ms
  ➜  Local:   http://localhost:5173/
  ➜  Network: http://192.168.1.100:5173/
  ➜  press h to show help
```

### 4.3 步骤 3：验证前后端联调

1. 打开浏览器，访问前端地址：`http://localhost:5173`
2. 在前端界面尝试「添加待办」「关联本地文件」等操作
3. 观察后端终端：若显示`Received request for tool: todo_manager`等日志，且前端正常返回结果，说明联调成功。

## 五、核心功能使用

### 5.1 基础待办管理

| 功能     | 操作路径（前端）                | 说明                                                             |
| -------- | ------------------------------- | ---------------------------------------------------------------- |
| 添加待办 | 首页 → 「添加待办」按钮         | 填写内容、选择分类（work/life/study）、优先级（high/medium/low） |
| 查询待办 | 首页 → 筛选栏（状态/分类）      | 支持按“未完成/已完成”状态、分类筛选                              |
| 更新待办 | 待办列表 → 目标任务「编辑」按钮 | 修改状态、内容、优先级等                                         |
| 删除待办 | 待办列表 → 目标任务「删除」按钮 | 删除后自动更新剩余任务索引                                       |
| 任务统计 | 首页 → 「统计报告」标签页       | 查看完成率、分类占比、文件关联数                                 |

### 5.2 文件关联功能（核心增强）

1. **添加文件关联**：
   - 添加待办时，点击「关联文件」按钮，选择本地文件（如 PDF、TXT）
   - 系统自动验证文件有效性，存储绝对路径（避免路径失效）
2. **查看文件关联**：
   - 待办列表中，带文件关联的任务会显示「文件图标」，hover 可查看路径
3. **更新/移除文件**：
   - 编辑待办时，重新选择文件（更新）或清空文件路径（移除）

### 5.3 智能提醒与统计

- **过期提醒**：首页顶部会显示过期任务警告，点击可快速定位
- **LLM 统计总结**：「统计报告」页会生成自然语言总结（如“您有 3 个待办，2 个关联了本地文件，建议优先处理高优先级工作任务”）

## 六、技术栈

### 6.1 NVIDIA 相关技术

- **NVIDIA NeMo Agent Toolkit**：后端核心框架，提供工具注册、LLM 适配、工作流调度
- **NeMo Function Registration**：实现`todo_manager`工具标准化接入
- **SPDX 规范**：代码版权与许可证管理（Apache-2.0）

### 6.2 后端技术

- Python 3.9+、uv（包管理）
- Pydantic（数据模型校验）
- FastAPI（NeMo Agent 内置 API 服务）

### 6.3 前端技术

- React（UI 框架）、Vite（构建工具）
- Axios（前后端接口请求）
- 响应式设计（支持 PC/平板端）

## 七、常见问题（FAQ）

### Q1：后端启动时提示“uv: 命令不存在”？

A1：需先安装`uv`包管理器：

```bash
pip install uv
```

### Q2：前端启动后无法连接后端？

A2：

1. 检查后端服务是否已启动（端口 8001 是否占用）
2. 确认前端配置的后端地址正确（通常在`src/api/config.js`中，需与后端`--host`和`--port`匹配）

### Q3：添加文件关联时提示“文件不存在”？

A3：

1. 确认选择的文件未被删除或移动
2. 若文件路径含中文/特殊字符，建议重命名后重试

### Q4：端口冲突怎么办？

A4：

- 后端：修改启动命令的`--port`参数（如`--port 8002`）
- 前端：修改`package.json`中`dev`脚本的端口（如`vite --port 5174`），并同步更新前端后端地址配置

## 八、版权声明

本项目基于 **Apache License 2.0** 协议开源，版权归 NVIDIA CORPORATION & AFFILIATES 及项目开发者所有。

- 允许非商业用途的修改、分发与使用
- 商业用途需联系 NVIDIA 获取授权
- 使用时需保留原始版权声明与协议文本

协议详情：[Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
