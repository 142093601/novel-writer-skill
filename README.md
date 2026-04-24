# Novel Writer — 三Agent协作短篇小说创作

基于 OpenClaw 的 AI 小说创作 Skill，通过 Planner → Reviewer → Writer 三个专业 Agent 协作，产出高质量短篇小说。

灵感来源：[PlotPilot（墨枢）](https://github.com/shenminglinyi/PlotPilot)

---

## 快速上手

### 安装

```bash
# 方式一：clawhub
clawhub install novel-writer

# 方式二：手动
git clone https://github.com/142093601/novel-writer-skill.git ~/.openclaw/skills/novel-writer
```

### 触发

在 OpenClaw 对话中输入以下任意一个触发词即可启动：

> `写小说` · `创作故事` · `写个短篇` · `帮我写小说` · `story` · `novel`

系统会引导你描述故事需求（题材、风格、章节数等），然后自动进入三 Agent 协作流程。

---

## 工作流程

整个创作分为 3 个阶段，全程由编排器（OpenClaw 主 Agent）调度：

```
阶段 0：需求收集
  └─ 与你对话，确认题材/风格/章节数/特殊要求 → 生成项目目录

阶段 1：大纲协作
  ├─ Planner 构思大纲 + 节拍表 + 情感弧线 + 角色关系图
  ├─ Reviewer 9维度审查大纲
  └─ 循环修改（最多3轮）→ 你确认大纲

阶段 2：逐章写作
  ├─ Writer 按节拍表写第X章
  ├─ Reviewer 结构审查（5项）
  ├─ Writer 修改（如需）
  ├─ Reviewer 质量审查（6项/100分制）
  └─ 你确认 → 更新 Story Bible → 下一章

阶段 3：全稿终审
  └─ Reviewer 全稿13项审查 → 合并交付
```

### 各 Agent 职责

| Agent | 职责 | 关键产出 |
|-------|------|----------|
| 📐 **Planner** | 大纲策划 | 故事梗概、章规划、节拍表、情感弧线、角色关系图、伏笔清单 |
| 🔍 **Reviewer** | 质量审查 | 大纲审查（9维）、结构审查（5项）、质量审查（6项/100分制）、全稿终审（13项） |
| ✍️ **Writer** | 正文撰写 | 按节拍逐章写作，严格 POV 控制，输出知识三元组和节奏数据 |

---

## 核心能力详解

### 节拍表（Beat Sheet）

每章被拆分为 3-5 个节拍，每个节拍包含：
- **场景**：在哪里发生
- **POV**：视角人物（Writer 严格遵守）
- **核心动作**：这个节拍要完成什么
- **情感目标**：读者应该感受到什么
- **张力等级**：1-10 的写作力度标尺

Writer 根据节拍表逐场景展开，不是自由发挥。

### POV 控制系统

每个节拍指定视角人物，Writer 遵守以下规则：
- 只能写 POV 角色的内心活动、感受、所见所闻
- 非 POV 角色只能通过外部行为和对话呈现
- 同一章 POV 切换不超过 2 次
- Reviewer 会审查 POV 一致性（有无内心独白泄露）

### 三维度张力分析

| 维度 | 权重 | 衡量内容 |
|------|------|----------|
| 情节张力 | 40% | 冲突强度、悬念密度、信息不对称 |
| 情绪张力 | 30% | 角色情绪波动、读者共情 |
| 节奏张力 | 30% | 场景切换、叙述速度、信息密度 |

每章写完后 Reviewer 输出三维度评分，编排器跟踪趋势，连续 3 章下降会报警。

### Story Bible（状态管理系统）

每部小说创建独立项目目录，所有状态存在 `story-bible/` 下：

```
novel-{书名}/
├── requirements.md          # 你的需求摘要
├── outline.md               # 确认版大纲
├── session-state.json       # 工作流状态（断点恢复用）
├── writing-context.md       # 叙事方向 + 审查反馈汇总
├── story-bible/
│   ├── characters.md        # 角色状态追踪 + 出场频率
│   ├── character-relations.md  # 角色关系矩阵 + 变化记录
│   ├── storylines.md        # 故事线追踪（主线/支线/角色弧）
│   ├── timeline.md          # 时间线
│   ├── plot-threads.md      # 伏笔追踪 + 潜台词台账
│   ├── world-rules.md       # 世界观规则
│   ├── style-guide.md       # 风格约定 + 角色典型台词
│   ├── beat-sheet.md        # 节拍表
│   ├── tension-curve.md     # 张力曲线 + 节奏数据
│   └── knowledge-base.md    # 知识三元组
├── archive/                 # 瘦身压缩的历史数据
└── chapters/
    ├── chapter-1.md
    └── ...
```

**Story Bible 是铁律** — Writer 不能违反其中的规则，Reviewer 审查时逐条对照。

### 审查评分体系

| 严重度 | 扣分 | 典型问题 |
|--------|------|----------|
| 严重 | -15 | 逻辑硬伤、Story Bible 违反、POV 泄露、情感严重偏离 |
| 警告 | -5 | 节奏偏慢、对话不自然、故事线停滞、出场频率异常 |
| 建议 | -2 | 局部可优化、陈词滥调 |

通过阈值：≥ 85 通过 / 70-84 建议修改 / < 70 需修改

### 文风漂移检测

Writer 每章输出文风量化指标，Reviewer 对比基准值检测漂移：

| 指标 | 异常阈值 |
|------|----------|
| 平均句长 | 偏差 > 30% |
| 对白占比 | 偏差 > 15pp |
| 形容词/副词密度 | 偏差 > 50% |

### 伏笔到期检测

编排器每章自动检查伏笔状态：
- **逾期**：预期回收章节已过但未回收 → 立即提醒
- **即将到期**：未来 3 章内 → 提示安排回收

### 潜台词台账

追踪角色之间"说出来的 ≠ 心里想的"的潜在线索：
- 记录表面关系 vs 真实态度
- Writer 通过细微行为体现，不直接点破
- Reviewer 审查潜台词线索一致性

---

## 项目目录详解

| 文件 | 说明 | 谁维护 |
|------|------|--------|
| `requirements.md` | 你确认的需求摘要 | 编排器 |
| `outline.md` | 最终确认的大纲 | Planner 产出，你确认 |
| `outline-draft.md` | 大纲草稿（迭代中） | Planner |
| `session-state.json` | 工作流状态快照 | 编排器（断点恢复用） |
| `writing-context.md` | 叙事方向 + 审查反馈汇总 | Planner 初始化，编排器维护 |
| `chapters/chapter-X.md` | 章节正文 | Writer 产出，你确认 |

### Story Bible 各文件详解

| 文件 | 内容 |
|------|------|
| `characters.md` | 每个角色的状态（位置/身体/情绪/信息）+ 出场频率统计 |
| `character-relations.md` | 角色关系矩阵 + 关系变化历史记录 |
| `storylines.md` | 故事线分类追踪（主线/支线/角色弧/感情线）+ 里程碑 |
| `timeline.md` | 故事内时间线（按章记录关键事件和时间跨度） |
| `plot-threads.md` | 伏笔注册表（ID、埋设章节、预期回收、状态）+ 潜台词台账 |
| `world-rules.md` | 世界观硬规则和禁区 |
| `style-guide.md` | 整体风格约定 + 每个角色的典型台词样本 |
| `beat-sheet.md` | 每章 3-5 个节拍（场景/POV/核心动作/情感目标/张力等级/预估字数） |
| `tension-curve.md` | 各章三维度张力评分 + 节奏数据（字数/对话占比/事件密度） |
| `knowledge-base.md` | 知识三元组（主体-关系-客体，故事事实记录） |

---

## 中断与恢复

如果对话中断（超时、网络断开），下次说"继续写小说"时会自动恢复：

1. 定位项目目录
2. 加载 `session-state.json` 精确定位进度
3. 从断点继续（大纲阶段 / 第X章写作 / 终审阶段）
4. 已确认的内容不会丢失

**恢复时会读取的文件（按顺序）：**
`session-state.json` → `writing-context.md` → `outline.md` → `story-bible/*` → `chapters/*`

---

## 高级机制

### Context 瘦身

Story Bible 会随章节增长而膨胀。编排器自动执行瘦身：
- 历史数据（前 80% 章节）压缩为 1-3 段摘要
- 近期数据（最近 20%）保留完整表格
- 完整版归档到 `archive/` 目录

各文件瘦身触发阈值：

| 文件 | 触发行数 |
|------|----------|
| characters.md | > 50 行 |
| timeline.md | > 30 行 |
| knowledge-base.md | > 40 条 |
| tension-curve.md | > 25 行 |
| storylines.md | > 40 行 |

### 宏观重构

当 Reviewer 连续 2 章给出"严重偏离大纲"评价，且原因是大纲本身不合理时，自动触发：
- Planner 分析偏差原因
- 对后续章节做增量调整（不是推翻重来）
- 你确认后继续写作

### 对话沙盒

以下情况触发对话打磨：
- 你要求"先写对话试试"
- 言情/权谋等对话密集题材（默认启用）
- Reviewer 连续 2 章评价"对话偏弱"

流程：提取关键对话场景 → Writer 写纯对话 → Reviewer 审查 → 迭代打磨 → 作为后续写作参考

---

## 最佳实践

### 需求收集阶段
- 越具体越好：不要只说"写个科幻"，要说"近未来赛博朋克，主角是数据清洁工，发现公司秘密"
- 特殊要求提前说：比如"要有反转结局""不要恋爱线""主角不能死"
- 章节数建议 5-10 章，每章 1500-3000 字效果最佳

### 大纲确认阶段
- 仔细看节拍表 — 这是 Writer 的执行蓝图
- 觉得不合理就改，不要等到写完再说
- 情感弧线决定了你读起来的感觉，确认它符合预期

### 逐章确认阶段
- 每章都给反馈，不要沉默跳过
- 如果觉得方向不对，及时说"改方向"
- Reviewer 的审查报告值得看，尤其是"警告"级别的问题

### 终审阶段
- 全稿终审会检查 13 项指标，包含伏笔闭合、知识三元组一致性、文风统一
- 如果终审发现问题，会自动进入修复流程

---

## 参考资料

skill 内置以下写作参考，Agent 写作时自动引用：

| 文件 | 用途 |
|------|------|
| `references/plot-design.md` | 情节模板、反转技法、冲突升级 |
| `references/character-design.md` | 角色档案模板、关系图谱 |
| `references/writing-techniques.md` | 开篇钩子、人物塑造、对话、爽点 |
| `references/worldbuilding.md` | 世界观构建（奇幻/科幻） |
| `references/branching.md` | 多结局分支设计 |
| `references/revision.md` | 改稿润色方法 |

---

## 目录结构

```
novel-writer/
├── SKILL.md                    # 编排器（完整工作流 + 调度逻辑 + 错误处理）
├── agents/
│   ├── planner.md              # Planner：大纲策划师
│   ├── reviewer.md             # Reviewer：质量评审师
│   └── writer.md               # Writer：小说撰写师
├── references/
│   ├── plot-design.md
│   ├── character-design.md
│   ├── writing-techniques.md
│   ├── worldbuilding.md
│   ├── branching.md
│   └── revision.md
└── README.md
```

---

## 许可证

MIT

### 致谢

节拍表、三维度张力分析、知识三元组、伏笔注册表、POV 控制、故事线追踪、文风指纹、潜台词台账等设计思路借鉴自 [PlotPilot（墨枢）](https://github.com/shenminglinyi/PlotPilot) 项目。
