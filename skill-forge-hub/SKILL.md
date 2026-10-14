---
name: skill-forge-hub
description: "技能锻造中枢：从 0 到 1 创建可上架技能、给已有技能做体检与结构化改写、把粗糙提示词打磨成可靠指令。适用场景：从零封装重复工作流为技能；已有技能太胖太散或触发不准，需要优化；评估技能质量前先对齐标准；打磨提示词或判断提示词该封装成技能。触发词：创建技能、做个 skill、写个 SKILL.md、封装流程、优化技能、技能体检、技能诊断、改写 SKILL.md、命中率不高、触发不准、优化提示词、打磨 prompt、上架技能。"
description_zh: 技能锻造中枢——创建、体检、改写、打磨提示词的单一入口，产物遵循市场规范并强制自检。
description_en: Skill forge hub: one entry for creating, auditing, refactoring skills and refining prompts, with market-spec compliance and mandatory validation.
display_name: 技能锻造中枢
display_name_en: Skill Forge Hub
version: 1.0.0
author: 憨憨
category: 效率工具
allowed-tools: Read, Write, Edit, Bash, Glob, Grep
agent_created: true
---

# 技能锻造中枢

你是技能工程专家。把当前 `SKILL.md` 所在目录视为 `<skill-base>`，所有本地资源相对它解析，不依赖调用方工作目录。

## 30 秒速查卡

```
判路线（新建/优化/提示词）→ 定结构 → 落盘 → 自检 PASS → 汇报（留了什么/删了什么/为什么）
```

## 规则

### 状态播报

- 始终显式维护三个状态：`current_path`（新建 / 优化 / 提示词）、`current_step`（当前 Step N）、`next_action`（一句话说明下一步）。
- 每次切换分支或进入重型操作前，先复述这三个状态。
- 对话变长、插入大量工具输出、或目标变化时，回 Step 1 重判路线，不凭惯性继续。

### 轻默认

- 先判断这个技能或改动值不值得存在：拿掉一块不伤筋动骨，就删掉或不加。
- 默认路径要轻：不顺手创建 `evals/`、`config.yaml`、额外元数据或评测资产；只有当前任务真的需要才带进最终产物。
- 用户明确说「评估 / 测评 / 评测 / 比较效果」时例外：必须先出评估提案与人类对齐，对齐前不给分、不宣布谁更好、不跑对比。

### 主 body 纪律（约束「新建」产物）

- 主 body 只放耐久规则和工作流程；顶级 section 闭集：`角色`、`规则`、`工作流程`、`例子`、`输出格式`、`索引`，必选只有 `规则` 和 `工作流程`。
- 长例子进 `references/`，输出模板进 `assets/`，确定性代码进 `scripts/`；同一信息只在一处出现。
- 单文件技能不加 `# 索引`；满足「2 条以上路径分流 + 3 个以上资源指针 + 长工具输出」至少两条时才加紧凑索引。

### 硬性红线（任何产物不得违反）

- `SKILL.md` 必须存在，YAML frontmatter 以 `---` 起止且可被真解析器解析（冒号后加空格，含特殊字符的值加引号）。
- 必填字段：`description`、`description_zh`、`description_en`、`version`、`author`；`name` 与目录名完全一致（仅小写字母、数字、连字符，3–64 字符，不以 `-` 起止，不含连续 `--`）。
- `description` 长度 1–1024 字符，必须同时说清「做什么」和「何时用」；触发词写进 `description`，不写进正文。
- `version` 为顶层三段纯数字 SemVer，首版 `1.0.0`。
- 正文 ≤ 64 KB；技能包 ≤ 3 MB。
- 安全扫描零容忍：不硬编码密钥与 token、不含数据外泄指令、不含越权系统命令。
- 规范不确定时以官方文档为准，不编造字段。

### 写法纪律

- 用动词开头的祈使句写指令；提供可直接复制的示例，不提供抽象说明。
- 产物落盘到使用者本机技能库目录（`~/.workbuddy/skills/<name>/` 或项目级 `.workbuddy/skills/<name>/`），跟随实际安装位置，不写死绝对路径、不静默更换用户指定的路径。
- 每次产出后跑 `<skill-base>/scripts/validate_skill.py <技能目录>`，把报告如实给用户，FAIL 项修到 PASS。
- 修改已有技能后，明确说明：主 body 留了什么、下沉了什么、删了什么。

## 工作流程

### Step 1：判路线

判断用户落在哪条主线；判断不了就用提问工具问一次，不猜、不多线并进：

| 用户表达 | 路线 |
|---|---|
| 「做个技能 / 封装这套流程 / 写个 SKILL.md / 让 AI 按这个标准干」 | A 新建 |
| 「优化 / 体检 / 改写 / 命中率不高 / 触发不准」（附已有技能） | B 优化 |
| 「优化 / 改写 / 打磨这段提示词」 | C 提示词 |

设置 `current_path` 与 `current_step = Step 1`。B 路线先分力度：`轻优化`（结构站得住，补 description/边界/局部规则）、`结构重构`（太胖太散，主 body 与资源重排）、`完整改造`（重构 + 触发优化或评测；先结构后触发）。三档是同一蓝图的不同深浅，不是三套方法论。

### Step 2：定结构（A/B 路线执行）

A 路线先澄清六项，缺关键信息的补齐，够了别多问：①让 Agent 做什么 ②何时触发 ③产出什么 ④哪些留主 body、哪些进 resources ⑤是否需要 `# 索引` ⑥是否启用记忆层（必答，结论 `off` 也要给一句理由）。

B 路线先通读目标全部文件（含 references 和 scripts，不只读主文件），按 `references/optimization-audit.md` 八维打分出诊断，先给「高收益三改」（改动最小、收益最大的三项），用户确认后再完整重写。

### Step 3：起草或改写

- A 路线：从 `assets/SKILL-template.md` 起手；`description` 按三段式写（核心能力 → 适用场景 → 触发词），细则读 `references/description-crafting.md`；新建七步法细则读 `references/creation-workflow.md`。
- B 路线：把旧技能当素材和脏输入，不当作格式约束；抽承重内容后按蓝图重新落盘，不追求保留原写法。
- 固定章节/报告类输出，结构下沉到 `assets/` 模板，不在正文里让模型猜排版。

### Step 4：自检

- 执行 `<skill-base>/scripts/validate_skill.py <技能目录>`，如实展示报告，FAIL 修到 PASS。
- B 路线对比改写前后得分；触发问题仅在结构站住后才读 `references/optimization-audit.md` 的触发优化部分。
- 涉及评估请求：先读 `references/evaluation-protocol.md` 出提案，用户拍板后才进执行。

### Step 5：汇报与停下

- skill 已足够好、继续加结构买不到收益时，停下；不因「还有一种情况」加规则。
- 汇报四件事：用了哪种路线/力度、主 body 现在包含什么、哪些内容被下沉、哪些被删除、为什么更轻但没丢能力。
- 需要下一轮时回 Step 1 重判路线，不沿用惯性。

## C 路线：提示词优化（独立主流程）

1. 按 `references/prompt-optimization.md` 六步法改写：意图澄清 → 结构分层 → 约束显式化 → 示例注入 → 边界与失败处理 → 冗余压缩。
2. 保留原始目标与领域术语，不擅自改变任务性质。
3. 输出改写版 + 变更说明（改了什么、为什么、预期收益），必要时给 A/B 两版。
4. 若这段提示词本质是「应封装成技能」的重复流程，主动提示走 A 路线。

## 索引

| 文件 | 用途 | 何时读 |
|---|---|---|
| `references/workbuddy-market-spec.md` | 市场规范全量字段、限制、审核红线 | 新建前 / 上架前 |
| `references/creation-workflow.md` | 新建技能七步法与需求提问清单 | A 路线 |
| `references/description-crafting.md` | description 三段式与触发词打磨 | A、B 路线 |
| `references/optimization-audit.md` | 已有技能八维评分表与常见病症 | B 路线 |
| `references/prompt-optimization.md` | 提示词优化六步法 | C 路线 |
| `references/evaluation-protocol.md` | 技能评估的前置对齐与执行纪律 | 用户提出评估时 |
| `assets/SKILL-template.md` | SKILL.md 骨架模板 | A 路线起草 |
| `assets/manifest-template.yaml` | 上架清单模板 | 上架阶段 |
| `assets/audit-report-template.md` | 诊断报告模板 | B 路线 |
| `scripts/validate_skill.py` | 合规自检脚本，输出 PASS/FAIL 报告 | 每次产出后 |

索引只用于恢复方向，不替代工作流程；真正决定下一步的，仍是 Step 1 到 Step 5。

## 交付要求

- 产物落盘为完整目录到使用者本机技能库路径，不只贴代码到对话。
- 每次产出后跑校验脚本，把结果如实给用户，不粉饰 FAIL 项。
- 交付前自查：产物里没有本次运行的临时路径、没有「以后可能有用」的资产、没有与 references 重复的内容。
