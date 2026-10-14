# 账本条目格式规范

三类账本文件的完整模板、ID 规则与状态流转。首次使用前先初始化账本，之后只追加、不覆盖。

## 初始化（仅首次，no-op 安全）

```bash
mkdir -p .learnings
[ -f .learnings/LEARNINGS.md ] || printf '# 学习记录\n\n开发过程中捕获的纠正、洞察与知识缺口。\n\n**类别**: correction | insight | knowledge_gap | best_practice\n\n---\n' > .learnings/LEARNINGS.md
[ -f .learnings/ERRORS.md ] || printf '# 错误日志\n\n命令失败与集成错误。\n\n---\n' > .learnings/ERRORS.md
[ -f .learnings/FEATURE_REQUESTS.md ] || printf '# 功能需求\n\n用户请求但尚不支持的能力。\n\n---\n' > .learnings/FEATURE_REQUESTS.md
```

目录/文件已存在时上述命令为 no-op。账本位置默认当前项目根目录 `.learnings/`，用户指定其他位置时从其指定。

## 学习条目（追加到 LEARNINGS.md）

```markdown
## [LRN-YYYYMMDD-XXX] category

**记录时间**: ISO-8601 时间戳
**优先级**: low | medium | high | critical
**状态**: pending
**领域**: frontend | backend | infra | tests | docs | config

### 摘要
一句话描述学到了什么

### 详情
完整上下文：发生了什么、错在哪里、正确做法是什么

### 建议行动
具体修复或改进措施

### 元数据
- 来源: conversation | error | user_feedback
- 相关文件: path/to/file.ext
- 标签: tag1, tag2
- 参见: LRN-20250110-001（若与已有条目相关）
- 出现次数: 1（可选）
- 首次出现: 2025-01-15（可选）
- 最近出现: 2025-01-15（可选）

---
```

**类别说明**：

| 类别 | 含义 |
|------|------|
| `correction` | 用户纠正的错误理解或做法 |
| `insight` | 非显而易见的发现 |
| `knowledge_gap` | 知识过时或缺失 |
| `best_practice` | 更优的工作方式 |

## 错误条目（追加到 ERRORS.md）

```markdown
## [ERR-YYYYMMDD-XXX] skill_or_command_name

**记录时间**: ISO-8601 时间戳
**优先级**: high
**状态**: pending
**领域**: frontend | backend | infra | tests | docs | config

### 摘要
简要描述失败内容

### 错误
```
实际错误信息或输出（脱敏后）
```

### 上下文
- 尝试的命令/操作
- 使用的输入或参数
- 相关环境信息
- 输出摘要（避免完整 transcript 与含敏感内容）

### 建议修复
若可识别，写出可能的解决方案

### 元数据
- 可复现: yes | no | unknown
- 相关文件: path/to/file.ext
- 参见: ERR-20250110-001（若重复出现）

---
```

## 功能需求条目（追加到 FEATURE_REQUESTS.md）

```markdown
## [FEAT-YYYYMMDD-XXX] capability_name

**记录时间**: ISO-8601 时间戳
**优先级**: medium
**状态**: pending
**领域**: frontend | backend | infra | tests | docs | config

### 请求能力
用户想要做什么

### 用户背景
为什么需要、解决什么问题

### 复杂度估计
simple | medium | complex

### 建议实现
如何构建、可扩展哪些现有能力

### 元数据
- 频率: first_time | recurring
- 相关功能: existing_feature_name

---
```

## ID 生成规则

格式：`TYPE-YYYYMMDD-XXX`

- `TYPE`：`LRN`（学习）、`ERR`（错误）、`FEAT`（功能）
- `YYYYMMDD`：当前日期
- `XXX`：当日序号（`001` 起递增）或 3 位随机字符（如 `A7B`）

示例：`LRN-20261015-001`、`ERR-20261015-A3F`

## 状态流转

| 状态 | 含义 | 何时设置 |
|------|------|----------|
| `pending` | 尚未处理 | 条目创建时 |
| `in_progress` | 正在处理 | 开始跟进时 |
| `resolved` | 已修复或已吸收 | 问题解决后，追加「解决记录」 |
| `wont_fix` | 决定不处理 | 决定关闭时，须在解决记录中说明原因 |
| `promoted` | 已晋升至项目记忆 | 蒸馏规则写入长期记忆文件后 |
| `promoted_to_skill` | 已提取为独立技能 | 技能生成并通过验证后 |

「解决记录」格式：

```markdown
### 解决记录
- **解决时间**: 2026-10-16T09:00:00Z
- **Commit/PR**: abc123 或 #42
- **备注**: 简要说明做了什么
```
