# skill-forge-hub · 技能锻造中枢

> 从 0 到 1 创建可上架技能、给已有技能做体检与结构化改写、把粗糙提示词打磨成可靠指令的单一入口。产物遵循市场规范并强制自检。

## 解决什么问题

技能创作里有三个断层：**创建无规范**（产物缺字段、过不了审）、**优化无标准**（太胖太散不知道砍哪、触发不准不知道修哪）、**评估无纪律**（凭感觉说谁好谁坏）。本技能把「判路线 → 定结构 → 落盘 → 自检 → 汇报」压缩成一条确定性工作流。

## 核心能力

| 能力 | 说明 |
|---|---|
| 三主线路由 | A 新建 / B 优化（轻优化·结构重构·完整改造三档力度）/ C 提示词打磨 |
| 状态播报 | `current_path` / `current_step` / `next_action` 显式维护，长对话随时可找回方向 |
| 结构纪律 | 主 body section 闭集（角色/规则/工作流程/例子/输出格式/索引），长内容下沉 references |
| 硬性红线 | frontmatter 必填字段、name 规范、三段式 description、64KB/3MB 上限、安全零容忍 |
| 强制自检 | 每次产出后跑 validate_skill.py，FAIL 修到 PASS，报告如实展示 |
| 评估先对齐 | 用户提出评估时先出提案与人类对齐，对齐前不给分、不跑对比 |

## 触发词

创建技能 · 做个 skill · 写个 SKILL.md · 封装流程 · 优化技能 · 技能体检 · 技能诊断 · 改写 SKILL.md · 命中率不高 · 触发不准 · 优化提示词 · 打磨 prompt · 上架技能

## 快速开始

```
用户：把咱们这套检索流程封装成一个技能
技能：A 路线 → 澄清六项 → assets/SKILL-template.md 起手 → description 三段式 → validate_skill.py → PASS → 汇报

用户：这个技能 600 行太胖了，帮我重构
技能：B 路线（结构重构）→ 通读全部文件 → 八维打分诊断 → 高收益三改 → 确认后重写 → 前后对比

用户：这段提示词效果不稳定，帮我打磨
技能：C 路线 → 六步法改写 → 输出改写版 + 变更说明
```

## 工作流（5 步 + C 线）

1. **判路线**——三条主线对照表；B 路线先分力度；设置状态播报三件套
2. **定结构**——A 澄清六项（含记忆层必答）；B 先诊断后动手
3. **起草或改写**——模板起手；旧技能当素材不当格式约束；固定结构下沉 assets
4. **自检**——validate_skill.py 如实展示；评估请求先走对齐提案
5. **汇报与停下**——四件事（路线/力度、留了什么、下沉了什么、删了什么）；够好就停

## 目录结构

```
skill-forge-hub/
├── SKILL.md                            # 技能主文件（规则 + 工作流 + 索引）
├── manifest.yaml                       # 上架清单
├── README.md                           # 本文件
├── assets/
│   ├── SKILL-template.md               # SKILL.md 骨架模板
│   ├── manifest-template.yaml          # 上架清单模板
│   └── audit-report-template.md        # 诊断报告模板
├── scripts/
│   └── validate_skill.py               # 合规自检脚本（PASS/FAIL 报告）
└── references/
    ├── workbuddy-market-spec.md        # 市场规范全量字段与审核红线
    ├── creation-workflow.md            # 新建七步法与需求提问清单
    ├── description-crafting.md         # description 三段式与触发词打磨
    ├── optimization-audit.md           # 八维评分表与常见病症
    ├── prompt-optimization.md          # 提示词优化六步法
    └── evaluation-protocol.md          # 评估前置对齐与执行纪律
```

## 依赖

- 本地文件读写 + Python 3（运行 validate_skill.py）
- 无网络请求、无凭据操作

## 质量与合规

- 已通过 WorkBuddy 技能校验：0 BLOCK / 0 WARN
- 冒烟测试通过：模板造夹具 + 校验脚本准确抓出植入的缺陷项
- 主文件 116 行 / 7.5 KB（上限 64 KB）

## 许可

许可证：MIT · 作者：憨憨
