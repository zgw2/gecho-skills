# TikTok Search SKILL 优化记录

基于《Claude Skills 开发完全指南》，对 `tiktok-search` 的 [SKILL.md](./SKILL.md) 进行了全面的重构与深度优化。本次优化将一个基础的工具说明文档，升级为了具备**多平台兼容、自我容错、严格约束**的高级 Agent SOP（标准作业程序）。

## 1. 基础信息规范化 (Frontmatter)
- **原版**：`name: TikTok Search`，`description` 仅说明了功能。
- **优化**：改为动名词 `name: searching-tiktok`。扩充了 `description`，增加了明确的触发场景词（Use when you need to...）。
- **价值**：符合官方命名规范，极大提升了大模型在规划阶段自动检索和命中该 Skill 的准确率。

## 2. 元数据与多平台兼容性 (Metadata)
- **原版**：仅支持 `openclaw` 平台的专属配置。
- **优化**：引入了**声明式多态（Declarative Polymorphism）**。保留了 `openclaw`，新增了 `hermes` 和标准的 `mcp` 命名空间配置。
- **价值**：实现跨平台兼容。无论挂载到 OpenClaw、HermesAgent 还是 Claude Desktop，支持读取对应配置的 Agent 都能找到对应的启动配置。

## 3. 服务初始化策略 (Service Initialization Strategy)
- **原版**：仅一句话提示“不要作为 shell 运行，服务会自动启动”。
- **优化**：
  - 构建了完整的决策树（Decision Tree）。
  - **引入原生 CLI 自动配置**：指导 Agent 在 HermesAgent 或 OpenClaw 下，直接执行宿主平台提供的原生 CLI 命令（如 `hermes mcp add...` 或 `openclaw mcp set...`）进行挂载。
  - 对于不支持命令行的平台（如 Claude Desktop），指导 AI 去寻找并修改底层配置文件（如 `claude_desktop_config.json`），并明确要求用户重启。
- **价值**：赋予 Agent “自我配置（Self-Bootstrapping）”能力。利用原生 CLI 配置是最安全、最高效的方式，彻底消除了修改 JSON 配置文件的解析风险，真正做到了无摩擦的“开箱即用”。

## 4. 工具防迷路与前缀适配 (Tool Naming Compatibility)
- **原版**：硬编码调用 `tiktok_search_top_200` 工具。
- **优化**：在 Input Format 中增加了**防迷路提示**，告知大模型在不同的宿主框架（如 Hermes）中，外部 MCP 工具会被自动加上前缀（如 `mcp_gecho_bridge_tiktok_search_top_200`）。
- **价值**：防止大模型在工具名称被宿主平台改写（加前缀）后，出现“找不到工具”的死循环报错。提升了跨平台的鲁棒性。

## 5. 环境准备自动化与角色边界 (Fail-Fast & Boundary Control)
- **原版**：要求安装好 Node.js 和 Chrome 扩展，未界定是谁的责任。
- **优化**：
  - 加入了环境缺失时的“主动响应策略”（如主动提示 `winget install...`）。
  - **明确角色边界（CRITICAL）**：明确指出配置浏览器和打开扩展是 **USER（用户）** 的责任。严禁 Agent 尝试使用自身的内置浏览器工具（如 Hermes 的 `browser_navigate`）去打开 TikTok。
- **价值**：防止大模型因为看到“需要 Chrome”，就“自作主张”地去调用内置浏览器跑偏任务，确保 Agent 只做自己该做的事（调用 MCP）。

## 6. 执行约束与防失控机制 (Execution Rules & CRITICAL Constraints)
- **原版**：无任何执行约束。大模型容易出现死循环、换工具兜底、伪造数据等问题。
- **优化**：新增了极其严厉的硬性约束（使用 MUST, NEVER 等强烈语气词）：
  - **Strict Tool Binding (Fail Fast)**：严禁工具失败或未就绪时，大模型自作聪明去换用通用 WebSearch 或内置的 `browser_navigate` 兜底。强制要求失败即停止，不提供任何替代方案。
  - **Anti-Hallucination**：严禁在超时或查无结果时伪造假数据。
  - **No Parallel Execution**：强制串行执行，防止多线程并发抢占 Chrome 导致崩溃。
  - **Rate Limiting & Anti-Spam**：限制重试次数（Max 1）和单轮并发数（Max 3）。
- **价值**：彻底根除了大模型在调用浏览器/爬虫类工具时最常见的灾难性行为，确保系统稳定。

## 6. 标准工作流与参数最佳实践 (SOP & Best Practices)
- **原版**：简单的 `query` 和 `save_dir` 参数说明，以及简陋的 Example。
- **优化**：
  - 强制要求 AI **主动**生成带时间戳的绝对路径来保存数据。
  - 制定了严格的 5 步 SOP（Pre-flight -> Determine Path -> Execute -> Process -> Report）。
  - 要求在聊天框中仅输出 3-5 条结果的 Markdown 表格摘要，避免 Raw JSON 刷屏撑爆上下文。
- **价值**：将防御性文档升级为主动引导，确保最终交付给用户的体验是克制、专业且数据安全的。

## 7. 故障排查决策树 (Troubleshooting Decision Tree)
- **原版**：无错误处理机制。
- **优化**：针对 "Chrome 插件未连接"、"风控/验证码导致超时" 等爬虫常见异常，预设了标准的用户提示话术。
- **价值**：当底层工具抛出晦涩错误时，Agent 能转化为人类可读的排障指南，形成闭环的反馈机制 (Feedback Loop)。

---
**结论**：优化后的 `SKILL.md` 完全遵循了“简洁为王”、“渐进式披露”、“适度自由”与“工作流模式”等最佳实践，是一个可以直接作为团队模板的高质量 Agent SOP。