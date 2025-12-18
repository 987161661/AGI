# 个人消费级 AGI 助手开发蓝图与执行清单 (2025版)

本蓝图基于**“功能性 AGI”**与**“工程化自迭代”**的理念，将项目拆解为四个可落地的里程碑。目标是在个人电脑算力约束下，通过**工具增强**、**外部记忆**与**周期性自我微调**，构建一个具备长期成长能力的智能体。

---

## 核心原则

1. **本地优先 (Local First)**：所有核心推理、记忆、工具必须能在离线环境下闭环。
2. **受控自研 (Controlled DIY)**：认知架构、记忆策略与安全沙箱必须自研；模型权重与基础设施优先开源。
3. **安全外壳 (Safety Shell)**：自我改造与工具执行默认遵循最小权限原则，配备严格的沙箱与门禁。
4. **昼夜节律 (Circadian Rhythm)**：区分“在线交互”与“离线学习”，以时间换取质量与成长。

---

## 阶段划分总览

| 阶段 | 周期 (预估) | 核心目标 | 交付物 |
| :--- | :--- | :--- | :--- |
| **V0: 基础设施** | 1-3 天 | 跑通本地 LLM 与开发环境 | 本地 API 服务、Python 骨架、Hello World |
| **V1: 增强助手** | 1-2 周 | 单智能体 + 工具 + 记忆 | 具备 RAG 与文件操作能力的 CLI/API 助手 |
| **V2: 认知架构** | 2-4 周 | 图式编排 + 多智能体 | Plan-Act-Critic 闭环，复杂任务成功率显著提升 |
| **V3: 自我进化** | 4-8 周 | 昼夜学习 + 受控重构 | 夜间微调流水线、自我改代码能力、回归评测门禁 |

---

## 详细执行清单

### 第一阶段 (V0)：基础设施搭建

目标：确保本地算力被正确调度，项目骨架就绪。

- [ ] **硬件与环境准备**
    - [ ] 确认显存/内存资源，选择合适量化版本 (如 Qwen2.5-32B-Int4 或 14B)。
    - [ ] 部署推理服务 (vLLM / llama.cpp)，暴露 OpenAI 兼容接口 (`/v1/chat/completions`)。
    - [ ] 配置 Python 开发环境 (`uv` / `conda`)，安装基础依赖 (`pydantic`, `httpx`, `sqlite3`)。

- [ ] **仓库初始化**
    - [ ] 建立标准目录结构 (`apps/`, `core/llm`, `core/tools`, `core/memory`)。
    - [ ] 实现 `LLMClient` 抽象，跑通与本地 API 的第一次对话。

---

### 第二阶段 (V1)：单智能体 + 工具 + 记忆 (RAG)

目标：构建一个能干活、有记忆的超级助手。

- [ ] **工具系统 (Core Tools)**
    - [ ] **接口定义**：实现 `Tool` 协议与 `ToolRegistry`。
    - [ ] **文件工具**：实现 `read_file`, `write_file`, `list_dir`，**必须包含路径白名单校验**。
    - [ ] **Shell 工具**：实现 `run_command`，**必须包含命令前缀白名单与超时控制**。
    - [ ] **Web 工具 (可选)**：实现基础的 `fetch_url` (无头浏览器或 requests)。

- [ ] **记忆系统 (Memory System)**
    - [ ] **Schema 设计**：在 SQLite 中建立 `runs`, `messages`, `tool_events`, `memory_items` 表。
    - [ ] **向量存储**：接入 Qdrant/Chroma 或本地 Faiss，实现文本 Embedding 与检索接口。
    - [ ] **RAG 策略**：实现“检索 -> 拼接 Prompt”的最小闭环。

- [ ] **Agent 核心 (The Loop)**
    - [ ] 实现基础 ReAct 循环 (Think -> Act -> Observe)。
    - [ ] 集成工具调用与 RAG 检索。
    - [ ] 添加基础日志记录 (记录所有 Prompt 与 Completion)。

---

### 第三阶段 (V2)：认知架构 (图式编排 + 多智能体)

目标：引入“慢思考”与“反思”，解决复杂多步任务。

- [ ] **状态机编排 (Graph Orchestration)**
    - [ ] **状态定义**：设计 `AgentState` (Goal, Plan, Steps, Scratchpad)。
    - [ ] **节点实现**：
        - [ ] `PlanNode`: 分解任务，生成子步骤。
        - [ ] `DecideNode`: 决定下一步是调用工具还是结束。
        - [ ] `ActNode`: 执行工具，捕获结果与错误。
        - [ ] `CriticNode`: 检查结果是否符合预期，决定是否回退/重试。
    - [ ] **路由逻辑**：连接节点，形成闭环图。

- [ ] **多智能体角色 (Personas)**
    - [ ] 设计 Planner Prompt (偏理性、结构化)。
    - [ ] 设计 Executor Prompt (偏代码、工具)。
    - [ ] 设计 Critic Prompt (偏挑刺、边界检查)。

- [ ] **结构化记忆 (Structured Memory)**
    - [ ] 增加 `facts` 表，用于存储三元组或关键实体属性。
    - [ ] 实现 `CommitMemoryNode`：在任务结束时自动总结并写入长期记忆。

---

### 第四阶段 (V3)：自我进化 (昼夜节律 + Gödel Agent)

目标：让系统通过经验自我优化，并具备受控的代码级重构能力。

- [ ] **评测门禁 (Evaluation Gate)**
    - [ ] **任务集构建**：编写 20-30 个核心任务 (yaml 格式)，包含预期结果与必须调用的工具。
    - [ ] **Runner 实现**：实现自动跑测脚本，输出成功率、耗时、工具调用次数。
    - [ ] **基线对比**：记录 V2 版本的指标作为 Baseline。

- [ ] **昼夜节律流水线 (Circadian Pipeline)**
    - [ ] **数据清洗**：从 SQLite 日志中提取成功轨迹，过滤敏感信息。
    - [ ] **样本生成**：(可选) 调用教师模型优化轨迹 (Refinement)。
    - [ ] **微调/训练**：集成 Unsloth/LoRA 脚本，对基座模型进行增量微调。
    - [ ] **模型热更**：微调后自动触发评测，通过后替换在线权重。

- [ ] **自我重构 (Self-Refinement)**
    - [ ] **专用工具**：实现 `apply_patch`, `run_tests`, `run_eval`。
    - [ ] **安全策略**：严格限制可修改的目录 (`core/` 部分模块)，禁止修改安全配置。
    - [ ] **工作流**：Introspection (发现问题) -> Proposal (生成 Patch) -> Verification (跑测) -> Approval (人工/自动合并)。

---

## 推荐技术栈 (2025 参考)

*   **语言**: Python 3.10+
*   **模型**: Qwen 2.5 (32B/14B Int4), Llama 3.x
*   **推理**: vLLM (Linux/WSL), llama.cpp (跨平台)
*   **编排**: LangGraph / 自研简易状态机
*   **向量库**: Qdrant / Chroma / SQLite-vss
*   **数据库**: SQLite (初期) -> PostgreSQL (后期)
*   **微调**: Unsloth / PEFT
