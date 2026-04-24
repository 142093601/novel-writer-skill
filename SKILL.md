---
name: novel-writer
description: |
  三Agent协作短篇小说创作。Planner构思大纲+节拍表+情感弧线+角色关系图 → Reviewer审查（9维度）→ Writer按节拍写作+知识三元组+文风量化 → Reviewer结构审查（5项）+质量审查（6项/100分制） → 循环至用户采纳。
  全程Story Bible追踪角色状态/时间线/伏笔/世界观/知识库/角色关系/信息差矩阵，三维度张力分析+伏笔到期检测+文风漂移检测+角色能力追踪，确保一致性。
  触发词：写小说、创作故事、写个短篇、帮我写小说、story、novel。
---

# Novel Writer — 三Agent协作短篇小说创作

## 架构总览

```
用户需求
   ↓
[编排器/你]
   ├─ 阶段1：大纲协作
   │   spawn → Planner 构思大纲 + 节拍表 + 情感弧线 + 角色关系图 + Story Bible 初始化（含8个文件）
   │   spawn → Reviewer 审查大纲（9维度）
   │   → 循环：Planner修改 → Reviewer再审（最多3轮）
   │   → 用户确认大纲
   │   → [可选] 对话沙盒
   │
   ├─ 阶段2：逐章写作
   │   spawn → Writer 按节拍表写第X章
   │   spawn → Reviewer 结构审查（5项）
   │   → Writer 修改（如需）
   │   spawn → Reviewer 质量审查（6项）
   │   → 用户确认 → 更新 Story Bible + 瘦身检查
   │   → 条件触发：宏观重构 / 对话沙盒
   │   → 循环下一章
   │
   └─ 阶段3：全稿终审
       spawn → Reviewer 全稿终审（结构+质量合并审查）
       → 合并交付
```

## Agent 角色

| 顺序 | Agent | 职责 | 角色定义文件 |
|------|-------|------|-------------|
| 1 | 📐 **Planner** | 大纲 + 节拍表 + 情感弧线 + 角色关系图 + Story Bible 初始化 | `agents/planner.md` |
| 2 | 🔍 **Reviewer** | 结构审查（5项）+ 质量审查（6项）+ 全稿终审 | `agents/reviewer.md` |
| 3 | ✍️ **Writer** | 按节拍写作 + 知识三元组 + 节奏数据 + 关系变化 | `agents/writer.md` |
| 4 | 📖 **Bible Keeper** | Story Bible 状态更新 + 监控报警 + 健康检查 + 瘦身标记 | `agents/bible-keeper.md` |

## Story Bible（状态管理系统）

每部小说创建独立项目目录，Story Bible 在其中维护：

```
workspace/
├── novel-{书名}/           ← 项目目录（每部小说独立）
│   ├── requirements.md     ← 用户需求摘要
│   ├── outline.md          ← 最终确认的大纲
│   ├── outline-draft.md    ← 大纲草稿（迭代中）
│   ├── session-state.json  ← 工作流状态快照（断点恢复用）
│   ├── writing-context.md  ← 叙事方向 + 审查反馈汇总
│   ├── story-bible/        ← Story Bible（由 Planner 初始化，编排器维护）
│   │   ├── characters.md       ← 角色状态追踪
│   │   ├── character-relations.md ← 角色关系图谱（关系矩阵 + 变化记录）
│   │   ├── storylines.md       ← 故事线追踪（主线/支线/角色弧 + 里程碑）
│   │   ├── timeline.md         ← 时间线
│   │   ├── plot-threads.md     ← 伏笔追踪 + 潜台词台账（含ID、状态、回收方式）
│   │   ├── world-rules.md      ← 世界观规则
│   │   ├── style-guide.md      ← 风格约定（含角色典型台词样本）
│   │   ├── beat-sheet.md       ← 节拍表（每章3-5个节拍，含张力等级）
│   │   ├── tension-curve.md    ← 章节质量追踪（张力+节奏合并）
│   │   ├── knowledge-base.md   ← 知识三元组（故事事实记录）
│   │   └── information-state.md ← 信息差状态追踪矩阵
│   ├── archive/            ← 归档目录（瘦身压缩的历史数据）
│   │   ├── characters-history.md
│   │   ├── timeline-archive.md
│   │   ├── knowledge-archive.md
│   │   └── tension-archive.md
│   └── chapters/
│       ├── chapter-1.md    ← 已确认的章节
│       └── ...
└── novel-{书名}.md         ← 最终整合文件
```

**Story Bible 是铁律** — Writer 不可违反其中的规则或状态，Reviewer 审查时必须对照。

新增 `information-state.md`：追踪信息差矩阵（每个信息条目在各角色中的知晓状态、揭露计划）。
新增能力追踪：在 characters.md 中增强角色能力/实力变化的详细记录。

## 参考资料

| 文件 | 用途 |
|------|------|
| `references/plot-design.md` | 情节模板、反转技法、冲突升级 |
| `references/character-design.md` | 角色档案模板、关系图谱 |
| `references/writing-techniques.md` | 开篇钩子、人物塑造、对话、爽点 |
| `references/worldbuilding.md` | 世界观构建（奇幻/科幻/现言/都市） |
| `references/emotion-curve.md` | 情绪曲线设计、情绪坐标体系 |
| `references/revision.md` | 改稿润色方法 |
| `references/scene-writing.md` | 场景级写作指南（段落结构/信息分层/微爽点/张力控制/声音指纹） |
| `references/genre-tropes.md` | 类型套路库（由 novel-analyzer 生成） |
| `references/information-asymmetry.md` | 信息差设计（三层模型/设计模板/管理矩阵/揭露时机） |
| `references/character-charm.md` | 角色魅力设计（五种吸粉类型/魅力四维/展示技法） |
| `references/action-scenes.md` | 动作场景写作（结构模板/描写技法/力量体系/战斗公平性） |
| `references/reader-psychology.md` | 读者心理学（爽感/悬念/共情机制/阅读疲劳/心理陷阱） |
| `references/romance-tension.md` | 感情线张力设计（推拉/暗恋/虐恋/破镜重圆/甜虐比例） |
| **精简版参考（嵌入 Agent 任务）** | |
| `references/slim/planner-lite.md` | Planner 专用速查（~2KB） |
| `references/slim/writer-lite.md` | Writer 专用速查（~2KB） |
| `references/slim/reviewer-lite.md` | Reviewer 专用速查（~2.5KB） |

---

## 执行流程

### 阶段 0：需求收集与项目初始化

#### Step 0.1：收集需求

与用户自然对话，收集（用户已给的不要重复问）：

1. **题材/类型** — 科幻、悬疑、言情、奇幻、现实、恐怖、武侠等
2. **核心创意** — 一句话故事核
3. **章节数量** — 用户说几章就几章
4. **每章字数** — 推荐 1500-3000 字，默认 2000
5. **风格/基调** — 轻松、沉重、讽刺、温暖、冷峻等
6. **主角数量** — 单主角/双主角/群像
7. **特殊要求** — 必须有/不能有的元素

整理成结构化需求摘要，发给用户确认。

#### Step 0.2：创建项目目录

确认需求后，在 workspace 下创建项目目录：

```bash
mkdir -p novel-{书名}/story-bible
mkdir -p novel-{书名}/chapters
mkdir -p novel-{书名}/archive
```

将需求摘要保存为 `novel-{书名}/requirements.md`。

#### Step 0.3：初始化会话状态

创建项目目录后，立即初始化以下文件：

**session-state.json** — 精确的工作流状态，用于断点恢复：

```json
{
  "project": "novel-{书名}",
  "created_at": "{ISO时间}",
  "last_updated": "{ISO时间}",
  "current_phase": "outline",
  "outline_status": "pending",
  "outline_round": 0,
  "current_chapter": 0,
  "total_chapters": {N},
  "completed_chapters": [],
  "chapter_status": {
    "current": null,
    "structure_review_round": 0,
    "quality_review_round": 0,
    "pending_feedback": null
  },
  "confirmed_outline": false,
  "user_feedback_queue": [],
  "notes": "",
  "slimming_log": []
}
```

**writing-context.md** — 叙事方向 + 审查反馈汇总（由 Planner 在大纲阶段初始化，编排器维护）：

```markdown
# 写作上下文

## 叙事方向

### 整体走向
{一句话描述从开篇到结尾的主线变化}

### 当前态势（第X章/共N章）
- 张力趋势：{上升/下降/平台}
- 情感方向：{当前读者应感受到什么}
- 角色弧进展：{主角目前在成长弧线的哪个阶段}

### 接下来3章的关键意图
- 第X章：{核心任务}
- 第X+1章：{核心任务}
- 第X+2章：{核心任务}

## 已确认的叙事决策
- {用户或AI做出的关键决策记录}

## 审查反馈汇总

### 已完成章节的审查记录
| 章节 | 审查轮次 | 主要问题 | 是否已解决 |
|------|----------|----------|------------|

### 反复出现的问题模式
- {如：角色对话偏正式、节奏偏慢等}

### 已确认的修改方向
- {用户明确要求的风格/方向调整}

## 宏观重构记录
（触发时补充）

## 对话沙盒记录
（触发时补充）
```

> **注意：** 每次启动新故事时，确保项目目录名唯一，不要覆盖已有项目。

---

### 阶段 1：大纲协作

#### Step 1.1：启动 Planner 构思大纲、节拍表、情感弧线与角色关系图

编排器先用 `read` 工具读取：
- `agents/planner.md`
- `references/slim/planner-lite.md`（精简版 ~2KB）

然后 spawn：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/planner.md 的完整内容}
    
    ## 大纲设计速查
    {嵌入: references/slim/planner-lite.md 的精简内容}
    
    ## 用户需求
    {需求摘要}
    
    ## 补充说明
    - 如需完整情节模板库、角色设计详细模板、世界观构建详情，可请求补充
    - 按你的角色定义输出：第一部分为大纲正文（含每章节拍表+每章情感目标+整体情感曲线+张力-情感对照表），第二部分为 Story Bible 初始化文件
```
```

编排器收到 Planner 输出后：
- 解析 `===STORY_BIBLE_INIT===` 到 `===END===` 之间的内容
- 将每个 `===FILE: xxx.md===` 的内容写入 `novel-{书名}/story-bible/xxx.md`
- 保存大纲正文到 `novel-{书名}/outline-draft.md`

**错误处理：** 如果 Planner 输出格式不符合预期（缺少分隔符、JSON 解析失败），编排器自行根据大纲正文手动初始化 Story Bible 各文件。

#### Step 1.2：启动 Reviewer 审查大纲

编排器先用 `read` 工具读取 `agents/reviewer.md`。

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/reviewer.md 的完整内容}
    
    ## 用户需求
    {需求摘要}
    
    ## 待审查的大纲
    {Planner 输出的大纲正文}
    
    ## 节拍表
    {beat-sheet.md 的完整内容}
    
    ## 情感弧线设计
    {大纲中的情感弧线部分}
    
    ## Story Bible 初始化
    {novel-{书名}/story-bible/ 各文件内容}
    
    请按9个维度审查大纲+节拍表+情感弧线。同时检查 Story Bible 各文件之间的内部一致性：
    - characters.md 的角色信息是否与 character-relations.md 的关系矩阵一致
    - plot-threads.md 的伏笔是否在 beat-sheet 中有对应节拍
    - storylines.md 的故事线里程碑是否在 beat-sheet 中有对应节拍
    - world-rules.md 的规则是否与大纲情节兼容
    - 每个 beat 是否都有明确的 POV 指定
    
    输出审查报告。
```

#### Step 1.3：判断审查结果

- **总评"通过"** → Step 1.5
- **"需修改"或"建议重做"** → Step 1.4

#### Step 1.4：修改循环（Planner → Reviewer）

编排器先用 `read` 工具读取 `agents/planner.md`、`agents/reviewer.md`。

```
第N轮循环：
  1. spawn Planner（附审查报告+上一版大纲+当前 Story Bible，嵌入 agents/planner.md）
     → 编排器解析输出，更新 outline-draft.md 和 story-bible/（含 beat-sheet.md、character-relations.md）
  2. spawn Reviewer（审查新大纲+新节拍表+新情感弧线+新 Story Bible，嵌入 agents/reviewer.md）
     → 判断：通过→Step 1.5 / 需修改→继续循环
```

**最多循环3轮**。第3轮仍需修改时：
- 编排器汇总所有轮次的审查意见
- 将3个版本的大纲+审查报告一起提交用户决定

#### Step 1.5：提交用户确认

```
📋 小说大纲（AI 评审通过）

{最终大纲正文}

💓 情感弧线
{情感曲线图 + 每章情感目标摘要}

📊 Story Bible 摘要
- 角色：X名主要角色
- 伏笔：X个（计划在第X章回收）
- 世界观：{简述}
- 节拍表：共X章 × 平均X个节拍 = X个写作单元

🔍 AI 评审摘要
- 综合评分：X/10
- 节拍表质量：X/10
- 主要亮点：{2-3条}
- 已修复问题：{简述修改过程中解决了什么}

评审轮次：X 轮

请确认：
✅ 确认采用 → 开始写作
✏️ 需要修改 → 告诉我哪里要改
🔄 重新构思 → 换个方向

> **📝 编排器须知：用户确认大纲后，立即更新以下文件：**
> 1. `session-state.json` → `current_phase: "writing"`, `confirmed_outline: true`, `current_chapter: 1`
> 2. `writing-context.md` → 从大纲中提取整体走向和前3章意图
> 3. 复制 `outline-draft.md` → `outline.md`（确认版）
```

#### 用户修改大纲时

如果用户选择"✏️ 需要修改"：

1. 记录用户的修改意见
2. 将修改意见合并到审查报告中（标注"用户要求"）
3. 回到 Step 1.4 循环（Planner → Reviewer）
4. **用户意见优先级最高** — 如果 Reviewer 意见和用户意见冲突，以用户为准
5. **用户修改最多2轮** — 第2轮用户仍不满意时，编排器汇总所有版本差异，与用户直接讨论确定最终方向

#### 用户选择"重新构思"时

如果用户选择"🔄 重新构思"：

1. 保留当前项目目录（归档为 `novel-{书名}-old/` 或在目录内加 `_archived` 标记）
2. 重新进入阶段 0，与用户讨论新的方向
3. 创建新的项目目录继续流程

---

### 阶段 1.5：对话沙盒（可选，大纲确认后）

> 对话沙盒是在正式写作前，单独打磨关键场景对白的步骤。解决"Writer 兼顾剧情推进时，对白质量打折扣"的问题。

#### 触发条件

以下任一条件满足时，编排器自动启动对话沙盒：

1. **用户要求** — 用户明确说"先写对话试试"或类似
2. **题材需要** — 言情、权谋等对话密集型题材，默认启用
3. **Reviewer 触发** — 阶段2中 Reviewer 连续2章给出"对话偏弱"评价时，暂停写作，回退执行对话沙盒

#### Step 1.7.1：选择对话场景

编排器从大纲中挑选 3-5 个**对话密集的关键场景**：
- 核心冲突场景（角色正面交锋）
- 情感爆发场景（告白/决裂/和解）
- 信息揭露场景（反转/真相）
- 权力博弈场景（谈判/审讯）

#### Step 1.7.2：启动 Writer 写纯对话

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/writer.md 的完整内容}
    
    ## 对话沙盒任务
    你不需要写完整章节，只需要写纯对话场景。
    
    ## 场景：{场景描述}
    - 参与角色：{角色}
    - 对话目的：{要达成什么——揭露信息/激化冲突/传递情感}
    - 对话张力：{X}/10
    
    ## 角色语言特征
    {style-guide.md 中的典型台词样本}
    
    ## Story Bible
    {角色当前状态 + 世界观规则}
    
    请只输出对话 + 必要的动作描写（不超过500字）。
    不需要输出完整的章节格式和 STATE_UPDATE。
```

#### Step 1.7.3：启动 Reviewer 审查对话

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/reviewer.md 的完整内容}
    
    ## 对话沙盒审查任务
    
    ### 场景描述
    {场景信息}
    
    ### 对话样本
    {Writer 输出的对话}
    
    ### 角色语言特征
    {style-guide.md 中的典型台词样本}
    
    请审查：
    1. 每个角色的说话方式是否符合其声线
    2. 对话是否自然（不像"念台词"）
    3. 对话是否有信息量（不是废话）
    4. 对话张力是否匹配要求
    5. 角色之间的语言差异是否明显（不能所有人都说一样的话）
    
    输出审查报告，标注需要调整的对白。
```

#### Step 1.7.4：迭代修改

- Writer 修改对话 → Reviewer 再审
- **每场景最多2轮**
- 通过后保存到 `novel-{书名}/story-bible/dialogue-sandbox/` 目录
- 编排器记录哪些场景的对话已打磨通过

#### 对话沙盒的价值

正式写作时（阶段2），编排器将已通过的对话样本传给 Writer 作为参考：
```
## 对话沙盒样本（已通过审查）
### 场景：{场景名}
{通过审查的对话内容}

Writer 写作时参考以上对话风格，但可根据剧情需要调整。
```

---

### 阶段 2：逐章写作循环

#### Step 2.1：启动 Writer 写作

编排器先用 `read` 工具读取：
- `agents/writer.md`
- `references/slim/writer-lite.md`（精简版 ~2KB）
- `references/revision.md`（仅修改稿时需要）

然后 spawn：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/writer.md 的完整内容}
    
    ## 写作技法速查
    {嵌入: references/slim/writer-lite.md 的精简内容}
    
    ## 通过审查的大纲（仅本章相关）
    {本章章节规划 + 结构说明}
    
    ## 本章任务：第X章
    {第X章的具体规划}
    
    ## 本章节拍表
    {第X章的节拍表，含 POV、场景、角色、核心动作、情感目标、张力等级、预估字数}
    
    ## 本章情感目标
    - 目标情绪：{情绪}
    - 触发点：{具体场景}
    - 强度要求：{X}/10
    - 强度标准：{达标的具体要求}
    - 节拍情感映射：{每个节拍对应的情绪}
    
    ## 角色当前状态
    {characters.md 中与本章相关角色的状态摘要}
    
    ## 角色关系
    {character-relations.md 中与本章相关的关系}
    
    ## 伏笔状态（本章需回收的）
    {plot-threads.md 中预期在本章或近期回收的伏笔}
    
    ## 潜台词台账（本章涉及角色的）
    {plot-threads.md 中与本章角色相关的潜台词条目}
    
    ## 世界观规则（摘要）
    {world-rules.md 关键规则，非世界构建类题材可省略}
    
    ## 风格约定
    {style-guide.md 中的叙事风格 + 本章出场角色的典型台词样本}
    
    ## 知识三元组（与本章相关）
    {从 knowledge-base.md 中筛选与本章角色/地点相关的三元组}
    
    ## 字数要求
    {每章字数}
    
    ## 前一章摘要
    {上一章结尾的关键信息，用于承接}
    
    请按你的角色定义输出：第一部分为章节正文，第二部分为结构化状态更新。
```
```

## 最近章节审查亮点（如非第1章）
```
## 前一章审查亮点（学习好的写法）
- {Reviewer 标注的亮点 1}
- {Reviewer 标注的亮点 2}
```

如果是**修改稿**，额外附上（精简上下文，不重复传完整大纲）：
```
## 上一版内容
{上一版章节内容}

## 修改意见
{Reviewer 审查报告}
{用户意见（如有）}

## 改稿技法参考
{嵌入: references/revision.md 的完整内容}
```

编排器收到 Writer 输出后：
- 保存章节正文到 `novel-{书名}/chapters/chapter-{X}.md`
- 解析 `===STATE_UPDATE===` 的 JSON 数据
- **如果是修改稿**：用最终确认版的 STATE_UPDATE 覆盖之前的暂存数据

**错误处理：** 如果 Writer 的 JSON 格式有误，编排器尝试从章节正文中手动提取关键状态信息，同时记录格式问题。

#### Step 2.2：启动 Reviewer 结构审查（第一轮）

编排器先用 `read` 工具读取 `agents/reviewer.md`。

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/reviewer.md 的完整内容}
    
    ## 本章结构审查任务
    
    ## 通过审查的大纲（仅本章相关部分）
    {本章章节规划 + 结构说明}
    
    ## 本章节拍表
    从 beat-sheet.md 中提取第X章：
    {第X章的节拍表}
    
    ## 待审查的第X章
    {Writer 输出的章节正文}
    
    ## Story Bible（审查前状态）
    {novel-{书名}/story-bible/ 各文件内容}
    
    ## 已有知识三元组
    {novel-{书名}/story-bible/knowledge-base.md 的当前内容}
    
    请执行第一轮：结构审查（5项）。检查：
    1. 大纲一致性 — 是否偏离大纲、关键情节是否遗漏
    2. 节拍执行 — 每个节拍的核心动作是否呈现
    3. 伏笔处理 — 该回收的伏笔是否回收、新埋是否合理
    4. 知识三元组一致性 — 是否与已记录事实矛盾
    5. 时间线/世界观一致性 — 时间顺序、规则遵守
    
    输出结构审查报告（含总评：通过/需修改）。
```

#### Step 2.3：结构审查判断

- **"通过"** → Step 2.5（质量审查）
- **"需修改"** → Step 2.4

#### Step 2.4：Writer 修改（结构问题）

将结构审查报告发回 Writer（同 Step 2.1，附加修改意见）。

返回 Step 2.2。**每章结构审查最多循环2轮**。超限后直接进入质量审查。

#### Step 2.5：启动 Reviewer 质量审查（第二轮）

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/reviewer.md 的完整内容}
    
    ## 本章质量审查任务
    
    ## 本章情感目标
    - 目标情绪：{情绪}
    - 目标强度：{X}/10
    - 强度标准：{达标要求}
    
    ## 待审查的第X章
    {Writer 输出的章节正文（修改后的最终版）}
    
    ## Story Bible
    {novel-{书名}/story-bible/ 各文件内容}
    
    请执行第二轮：质量审查（6项），采用 100 分制扣分制（严重 -15，警告 -5，建议 -2）。检查：
    1. 文字质量 — 是否有水文、描写是否生动
    2. 三维度张力评分 — 情节张力/情绪张力/节奏张力各 0-100，加权综合
    3. 文风一致性 — 角色对白是否符合声线 + 文风量化指标（句长/对白占比/修饰密度漂移检测）
    4. 节奏审计 — 字数/对话比/事件密度
    5. 情感目标达成 — 是否实现了本章情感目标
    6. 章末钩子 — 是否有悬念引向下章
    
    通过阈值：≥ 85分通过，70-84建议修改，<70需修改。
    
    Writer 输出的 voice_metrics（avg_sentence_length/dialogue_turns/adj_adv_density）用于文风量化对比。
    
    输出质量审查报告（含三维度张力、审查评分、文风量化、伏笔状态）。
```

#### Step 2.6：质量审查判断

- **"通过"** → Step 2.8
- **"需修改"** → Step 2.7

#### Step 2.7：Writer 修改（质量问题）

将质量审查报告发回 Writer（同 Step 2.1，附加修改意见）。

返回 Step 2.5。**每章质量审查最多循环1轮**。超限后直接进入用户确认。

#### Step 2.8：Bible Keeper 更新 Story Bible + 健康检查

编排器先用 `read` 工具读取 `agents/bible-keeper.md`，然后 spawn：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/bible-keeper.md 的完整内容}
    
    ## 本章数据
    
    ### Writer 的 STATE_UPDATE
    {解析后的 STATE_UPDATE JSON}
    
    ### Reviewer 审查结果
    - 总评：{通过/需修改}
    - 评分：{XX}/100
    - 主要问题：{列出严重和警告问题}
    
    ### 当前 Story Bible
    ### 角色状态
    {characters.md 内容}
    ### 角色关系
    {character-relations.md 内容}
    ### 时间线
    {timeline.md 内容}
    ### 伏笔
    {plot-threads.md 内容}
    ### 故事线
    {storylines.md 内容}
    ### 知识库
    {knowledge-base.md 内容}
    ### 信息差状态
    {information-state.md 内容}
    ### 张力曲线
    {tension-curve.md 内容}
    ### 写作上下文
    {writing-context.md 内容}
    ### 会话状态
    {session-state.json 内容}
    
    ### 进度信息
    - 本章：第{X}章
    - 总章节：{N}章
    - 字数要求：{X}字
    
    请更新 Story Bible 各文件，输出健康报告。
```

编排器收到 Bible Keeper 输出后：

**处理第一部分（Bible 更新）：**
- 解析 `===BIBLE_UPDATE===` 到 `===END===` 之间的内容
- 将每个 `===FILE: xxx.md===` 的内容写入对应文件
- `===FILE: session-state.json===` 写入 `session-state.json`
- 如有 `===FILE: archive/xxx-archive.md===`，写入归档目录

**处理第二部分（健康报告）：**
- 解析 `===HEALTH_REPORT===` 的 JSON
- 如有报警，展示给用户并在 `writing-context.md` 的审查反馈中记录
- 如 `context_health.slimming_needed` 非空，编排器执行瘦身操作：
  1. 将完整原始内容备份到 `archive/` 目录
  2. 根据瘦身规则压缩文件（详见 bible-keeper.md 的瘦身模板）
  3. 在 `session-state.json` 中记录瘦身事件

**编排器瘦身操作（当 Bible Keeper 标记需要瘦身时）：**

编排器直接执行，不需要 spawn Agent：
1. 读取超限文件的完整内容
2. 将完整版复制到 `archive/{文件名}-archive.md`
3. 按瘦身模板压缩：历史数据→摘要，近期数据→详细保留
4. 写回瘦身版

| 文件 | 阈值 | 瘦身方式 |
|------|------|----------|
| characters.md | > 50 行 | 前 80% 角色状态压缩为 1-3 句摘要，保留最近 20% 详细表格 |
| timeline.md | > 30 行 | 前 80% 压缩为一段叙事摘要，保留最近 20% 详细记录 |
| knowledge-base.md | > 40 条 | 只保留与当前写作角色/地点相关的三元组，其余归档 |
| tension-curve.md | > 25 行 | 只保留最近 15 章详细数据，更早的保留均值摘要 |
| storylines.md | > 40 行 | 已完结的故事线压缩为 1 句摘要，仅保留进行中的详细追踪 |

#### Step 2.9：提交本章给用户

```
📖 第X章：《章节名》

{章节正文}

---
📝 本章摘要：{从 Writer 输出提取}
💓 情感达成：{实际情绪} (目标：{目标情绪} {目标强度}/10)
📈 本章张力：情节{X} 情绪{X} 节奏{X} → 综合{X}/10
🔍 AI 审查：{通过/需修改}（评分 XX/100，结构审查 X 轮 + 质量审查 X 轮）
📊 累计张力趋势：{简述近3章的综合张力变化}
⏱️ 节奏数据：{字数}字 / 对话{X}% / 事件密度{高/中/低}
🖋️ 文风状态：{稳定/轻微偏移/明显漂移}
👁️ POV：{本章视角人物}，切换 {X} 次
🔮 信息差：活跃 {X} 个，本章揭露 {X} 个
📖 故事线推进：{主线/支线/角色弧推进情况摘要}

请确认：
✅ 满意，继续下一章
✏️ 需要修改 → 告诉我哪里要改
🔄 改方向 → 说明新想法
```

#### 用户修改章节时

如果用户选择"✏️ 需要修改"：
1. 记录用户修改意见
2. 下一轮 Writer 写作时附上用户意见（优先级高于 Reviewer 意见）
3. 修改后重新走 Reviewer 结构审查 + 质量审查

#### Step 2.10：循环下一章

重复 Step 2.1 - 2.9，直到所有章节完成。

---

#### Step 2.11：宏观重构（条件触发）

> 宏观重构不是推翻重来，而是从全局视角审视已写内容，对后续大纲做增量调整。

##### 触发条件

以下条件**同时满足**时自动触发：

1. Reviewer 在最近 2 章审查中均给出"严重偏离大纲"的评价
2. 偏离原因不是 Writer 的执行问题，而是**大纲本身不合理**（如：角色发展自然走向与大纲预设冲突）

或者用户主动要求："重新审视一下后面怎么写"。

##### Step 2.11.1：启动宏观分析

编排器先用 `read` 工具读取 `agents/planner.md`，然后 spawn：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/planner.md 的完整内容}
    
    ## 宏观重构任务
    
    ### 当前大纲
    {outline.md 完整内容}
    
    ### 已完成章节（实际内容）
    第1章：
    {chapter-1 内容摘要}
    ...
    第X章：
    {chapter-X 完整内容}
    
    ### 审查反馈
    {最近2章的审查报告，标注为什么"严重偏离"}
    
    ### 叙事方向
    {writing-context.md 中的叙事方向部分}
    
    ### 张力曲线
    {tension-curve.md 当前内容}
    
    请分析：
    1. 实际写作与大纲的偏差在哪里
    2. 偏差的原因是什么（角色自然发展？情节逻辑需要？大纲本身缺陷？）
    3. 后续大纲应如何调整（增量修改，不是重写）
    
    输出：
    - 诊断报告（偏差分析 + 原因）
    - 修改建议（具体到哪些章节规划需要调整）
    - 修改后的后续大纲（仅修改受影响的章节）
    - 更新后的节拍表（仅修改受影响的章节）
```

##### Step 2.11.2：编排器执行重构

收到 Planner 输出后：
1. 将诊断报告展示给用户
2. 如果用户同意修改方案：
   - 更新 `outline.md`（仅修改后续章节）
   - 更新 `beat-sheet.md`（仅修改受影响的节拍）
   - 更新 `writing-context.md`（调整叙事方向）
   - 更新 `session-state.json`（记录重构事件）
   - 从断点继续写作（下一章使用新大纲）
3. 如果用户不同意：
   - 记录用户意见
   - 继续按原大纲写作，但 Writer 需要更忠实于大纲

##### 重构记录

编排器在 `writing-context.md` 中记录：
```markdown
## 宏观重构记录
- 触发时间：第X章写作后
- 触发原因：{简述}
- 调整内容：{修改了哪些章节规划}
- 用户确认：[是/否]
```

---

#### Step 2.12：对话沙盒补充触发（条件触发）

> 如果阶段2中 Reviewer 发现对话质量连续偏低，暂停写作，插入对话沙盒。

##### 触发条件

Reviewer 在审查中给出以下评价：
- "角色对话偏弱" 或 "对话不符合角色声线"
- 且文风漂移检测标记为"有偏离"
- **连续2章**出现以上情况

##### 执行流程

1. 暂停当前章节写作
2. 从当前章节中提取对话密集的场景
3. 执行阶段 1.5 的对话沙盒流程（Step 1.7.1 - 1.7.4）
4. 将打磨通过的对话样本作为 Writer 的参考
5. 重新写当前章节

##### 预防机制

如果对话沙盒在阶段 2 被触发，编排器在 `writing-context.md` 中记录：
```markdown
## 对话沙盒记录
- 触发时间：第X章审查后
- 触发原因：{简述}
- 沙盒场景：{哪些场景进行了对话打磨}
- 后续影响：Writer 此后写作需参照沙盒样本
```

---

### 阶段 3：全稿终审与交付

#### Step 3.1：全稿终审

所有章节确认后，编排器先用 `read` 工具读取 `agents/reviewer.md`，然后启动 Reviewer 做最终全局审查：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/reviewer.md 的完整内容}
    
    ## 全稿终审任务
    
    ### 大纲
    {最终大纲}
    
    ### 情感弧线
    {大纲中的情感弧线部分}
    
    ### 节拍表
    {beat-sheet.md 内容}
    
    ### 张力曲线
    {tension-curve.md 内容}
    
    ### 全部章节
    第1章：
    {chapter-1 内容}
    
    第2章：
    {chapter-2 内容}
    
    ...（所有章节）
    
    ### 最终 Story Bible
    {story-bible/ 各文件最终状态}
    
    ### 知识库
    {knowledge-base.md 内容}
    
    请做全稿终审，重点检查：
    1. 第1章与最终章的角色状态是否连贯
    2. 全书时间线是否正确
    3. 所有伏笔是否已回收（输出伏笔闭合总览），潜台词是否已揭露
    4. 世界观规则全程是否一致
    5. 情感曲线是否完整实现（对照大纲设计的情感弧线，评估每章情感目标的达成度）
    6. 章节之间的过渡是否自然
    7. 张力曲线总览（最高/最低/异常区域，三维度分别评估）
    8. 节奏曲线总览（是否前紧后松？中段有无塌陷？字数趋势是否稳定？）
    9. 知识三元组全书无矛盾
    10. 角色文风全书统一
    11. 角色关系变化是否自洽
    12. 各故事线（主线/支线/角色弧）是否完整闭合
    13. 角色出场频率是否合理（有无重要角色被遗忘）
    
    输出全稿终审报告。
```

#### Step 3.2：Bible Keeper 最终状态同步

终审通过后，spawn Bible Keeper 做最终状态同步：

```
sessions_spawn:
  runtime: "subagent"
  mode: "run"
  task: |
    {嵌入: agents/bible-keeper.md 的完整内容}
    
    ## 最终状态同步任务
    这是全稿终审通过后的最终 Bible 同步。
    
    请检查：
    1. 所有伏笔是否已标记为回收（对照 plot-threads.md）
    2. 所有故事线是否已标记为完结（对照 storylines.md）
    3. 角色状态是否与终章一致
    4. 生成最终健康报告
    
    输出更新后的最终 Bible 文件。
```

编排器更新所有 Bible 文件为最终版。

#### Step 3.3：修复问题（如有）

如果终审发现严重问题：
1. 确定问题所在的具体章节
2. 针对该章节启动 Writer 修改 + Reviewer 审查（同阶段2流程）
3. 修改后重新终审

**终审最多循环2轮**。

#### Step 3.4：整合与交付

1. 按顺序合并所有章节为一个完整文件
2. 添加标题、目录（可选）
3. 保存为 `novel-{书名}.md`
4. 通知用户：

```
🎉 小说创作完成！

📄 文件：novel-{书名}.md
📊 共 {X} 章，约 {XXXX} 字
💓 情感曲线：{简述从开头到结尾的情绪走向}
📈 张力峰值：第X章（{X}/10）
📉 张力低谷：第X章（{X}/10）
📋 伏笔状态：{X} 个全部回收 / {X} 个未回收（说明原因）
🧩 知识三元组：共 {X} 条
📖 故事线：主线 1 条完结 / 支线 X 条完结 / 角色弧 X 条完结

如需修改任何章节，直接告诉我。
```

---

## 编排器操作手册

### 状态管理

编排器全程维护以下状态（存在对话上下文中）：

| 状态 | 说明 | 更新时机 |
|------|------|----------|
| 需求摘要 | 用户确认的需求 | 阶段0，之后不变 |
| 项目目录路径 | `novel-{书名}/` | 阶段0创建 |
| 当前大纲 | 最终确认版 | 阶段1完成时 |
| 当前节拍表 | 最终确认版 | 阶段1完成时 |
| 当前章节号 | 正在写第几章 | 每章开始时 |
| 已完成章节 | 确认的章节列表 | 每章确认后 |
| Story Bible 状态 | 各文件的当前内容 | 每章后更新 |
| 张力曲线 | 各章三维度张力值记录 | 每章后更新 |
| 故事线状态 | 各故事线进展 | 每章后更新 |
| 出场频率 | 各角色出场统计 | 每章后更新 |
| 循环计数器 | 当前处于第几轮修改 | 每轮修改时 |
| 会话状态快照 | session-state.json 的当前值 | 每个关键步骤后 |
| 写作上下文 | writing-context.md 的内容 | 每章后更新 |
| 健康报告 | Bible Keeper 的报警和摘要 | 每章后更新 |

### 子 Agent 调用规范

**角色定义嵌入：** 每次 spawn 时，必须将对应 `agents/*.md` 的完整内容嵌入 task 开头。

**参考资料嵌入（精简优先）：** 子 Agent 无法读取 skill 目录下的文件。编排器使用 `references/slim/` 下的精简版（~2-3KB），而非全量版：

| Agent | 精简版引用 | 全量版（仅补充时用） |
|-------|-----------|-------------------|
| Planner | `slim/planner-lite.md` | `plot-design.md`, `character-design.md`, `worldbuilding.md`, `emotion-curve.md` |
| Writer | `slim/writer-lite.md` | `writing-techniques.md`, `revision.md`（仅修改时） |
| Reviewer | `slim/reviewer-lite.md` | 不需要全量版 |
| Bible Keeper | `agents/bible-keeper.md`（已含所有规则） | 不需要全量版 |

**全量版触发条件：** 当 Agent 在精简版基础上仍需要更多细节时（如 Planner 需要完整的世界观模板），编排器补充嵌入对应全量文件。

**Bible 数据嵌入策略：** Writer 只需收到与本章相关的 Bible 摘要（角色状态、关系、伏笔、风格约定），不需要全部 10 个文件的完整内容。Bible Keeper 负责在写作后更新所有文件。

**上下文传递：** 子 Agent 无状态，每次必须传入：
- 它的角色定义（agents/*.md 完整内容）
- 它的任务指令
- 所需的上下文数据（大纲、节拍表、Story Bible、上一版内容等）

**上下文精简策略（防止 task 过长）：**

**注意：传入 Agent 的 bible 文件均为瘦身后的版本**（历史摘要 + 近期详细）。完整版归档在 archive/ 目录，仅全稿终审时恢复使用。
- 大纲阶段：传完整大纲 + 完整 Story Bible + 完整参考资料
- 单章写作：传完整大纲 + **仅本章规划 + 本章节拍表** + 本章情感目标 + Story Bible + 相关知识三元组 + 写作技法参考
- 单章修改：传**仅本章规划 + 本章节拍表** + 上一版内容 + 审查意见 + 用户意见 + Story Bible（不重复传完整大纲）
- 全稿终审：传完整大纲 + 节拍表 + 张力曲线 + 所有章节 + Story Bible（此场景必须全量）

### 循环控制

| 环节 | 最大轮次 | 超限处理 |
|------|----------|----------|
| 大纲协作 | 3 轮（每轮：Planner→Reviewer） | 汇总3版+审查意见，交用户决定 |
| 每章结构审查 | 2 轮 | 直接进入质量审查 |
| 每章质量审查 | 1 轮 | 直接进入用户确认 |
| 全稿终审 | 2 轮 | 交付当前版本，标注未修复问题 |

### 错误处理

| 情况 | 处理方式 |
|------|---------|
| 子 Agent spawn 失败 | 重试1次，仍失败则告知用户 |
| 子 Agent 输出不完整 | 用已有内容继续，标注"输出可能不完整" |
| Writer JSON 格式错误 | 编排器手动从正文中提取状态信息 |
| Planner 格式不符预期 | 编排器手动初始化 Story Bible |
| 上下文过长 | 使用精简参考（slim/），只传与本章相关的 Bible 摘要，不传全文大纲 |
| Bible Keeper 输出格式错误 | 编排器从 STATE_UPDATE 手动更新 Bible 文件（保留旧的直接更新能力作为兜底） |


### Context 瘦身机制

防止 Story Bible 和知识库文件随章数增长无限膨胀，导致 Agent context 过长、注意力分散。

#### 瘦身原理

**核心思路：旧数据压缩为摘要，近期数据保留详细，当前数据全量。**

传给 Agent 的 task 中，bible 文件的内容分为两层：
- **历史摘要**（前 80% 的章节）→ 压缩为 1-3 段话
- **近期详细**（最近 20% 的章节）→ 保留完整表格

Agent 只看到压缩后的版本，完整历史归档到 `archive/` 目录（可追溯但不传入 task）。

#### 触发条件

每章写作完成后（Step 2.8），编排器检查以下文件是否超限：

| 文件 | 触发行数 | 瘦身方式 |
|------|----------|----------|
| characters.md | > 50 行 | 前 80% 角色状态压缩为 1-3 句摘要，保留最近 20% 详细表格 |
| timeline.md | > 30 行 | 前 80% 时间线压缩为一段叙事摘要，保留最近 20% 详细记录 |
| knowledge-base.md | > 40 条 | 只保留与当前写作角色/地点相关的三元组，其余归档 |
| tension-curve.md | > 25 行 | 只保留最近 15 章详细数据（含三维度），更早的只保留均值摘要 |
| storylines.md | > 40 行 | 已完结的故事线压缩为 1 句摘要，仅保留进行中的详细追踪 |

#### 瘦身执行流程

当某个文件触发瘦身时：

1. **归档**：将完整原始内容复制到 `novel-{书名}/archive/{文件名}-archive.md`
2. **压缩**：将文件改写为瘦身版（历史摘要 + 近期详细）
3. **记录**：在 `session-state.json` 中记录 `"slimming_log": ["characters.md @ 第X章"]`

**编排器自行执行瘦身，不需要 spawn Agent。** 瘦身是纯文本操作，编排器直接读文件→压缩→写回。

#### 各文件瘦身模板

**characters.md 瘦身示例：**

```markdown
# 角色状态追踪

## 主角 — 历史摘要（第1-40章）
林远从青云村少年成长为天穹宗内门弟子。关键转折：第12章拜师玄清、第28章师父为救他牺牲、
第38章发现自己是魔族后裔。当前实力：金丹初期。主要关系：与苏瑶从敌对变为盟友。

## 主角 — 近期状态（第41-50章）
| 章节 | 位置 | 身体 | 情绪 | 信息 |
|------|------|------|------|------|
| 第41章 | 天机城 | 轻伤 | 焦虑 | 知道反派计划 |
| ... | | | | |
| 第50章 | 魔域入口 | 满状态 | 决然 | 准备决战 |
```

**timeline.md 瘦身示例：**

```markdown
# 时间线

故事起始时间：天元历3024年春

## 历史摘要（第1-35章，历时约3个月）
林远从青云村出发，途经天机城、迷雾森林，拜师玄清后入天穹宗。
期间经历了入门考核、秘境试炼、宗门大比等事件。
师父玄清在第28章的魔族袭击中牺牲。

## 近期记录（第36-50章）
| 章节 | 故事内时间 | 关键事件 |
|------|------------|----------|
| 第36章 | 第4个月初 | 林远闭关突破金丹 |
| ... | | |
```

**knowledge-base.md 瘦身示例：**

```markdown
# 知识三元组

## 归档说明
第1-30章的三元组已归档至 archive/knowledge-archive.md（共85条）。
当前仅保留与第31-50章活跃角色/地点相关的三元组。

## 当前三元组（与当前写作相关）
| 主体 | 关系 | 客体 | 出现章节 |
|------|------|------|----------|
| 林远 | 持有 | 玄清遗剑 | 第28章 |
| ... | | | |
```

**tension-curve.md 瘦身示例：**

```markdown
# 章节质量追踪（张力 + 节奏）

## 历史摘要（第1-35章）
- 平均张力值：6.2/10
- 平均字数：2050字/章
- 平均对话占比：38%
- 平均事件密度：中
- 异常章节：第12章（对话占比68%）、第25章（字数仅1400）

## 近期数据（第36-50章）
| 章节 | 张力值 | 趋势 | 字数 | 对话占比 | 事件密度 | 状态 | 备注 |
|------|--------|------|------|----------|----------|------|------|
| 第36章 | 6/10 | ↑ | 2100 | 35% | 中 | ✅ | |
| ... | | | | | | | |
```

#### 瘦身效果预估

| 章节范围 | 瘦身前 bible 总量 | 瘦身后 | 节省 |
|----------|-------------------|--------|------|
| 第30章 | ~8k tokens | ~5k | ~30% |
| 第50章 | ~12k tokens | ~5k | ~58% |
| 第80章 | ~18k tokens | ~6k | ~67% |
| 第100章 | ~22k tokens | ~6k | ~73% |

#### 全稿终审例外

阶段 3 全稿终审时，传入**完整未瘦身的 bible 文件**（从 archive/ 恢复）。终审需要全局视角，不能用压缩版。


### 中断恢复协议

会话中断（60分钟超时、网络断开等）后重新接入时，编排器必须执行以下恢复流程：

#### 恢复 Step 1：定位项目

```
用户说"继续写小说"或类似意图时：
1. 搜索 workspace 下 novel-*/ 目录
2. 如果有多个项目，列出让用户选择
3. 确定目标项目目录 novel-{书名}/
```

#### 恢复 Step 2：加载会话状态

**必须按以下顺序读取文件（不能跳过任何一个）：**

```
1. session-state.json          ← 精确定位：我在哪个阶段、第几章、第几轮
2. writing-context.md           ← 叙事方向 + 审查反馈：我要往哪走 + 什么问题反复出现
3. outline.md / outline-draft.md ← 大纲蓝图
4. story-bible/ 全部文件        ← 静态状态
5. chapters/ 已有文件           ← 已完成内容
```

#### 恢复 Step 3：根据状态决定恢复点

读取 `session-state.json` 的 `current_phase` 字段：

| 值 | 含义 | 恢复动作 |
|----|------|----------|
| `"outline"` | 中断在大纲阶段 | 读取 outline-draft.md，从上次停下的审查轮次继续 |
| `"writing"` | 中断在写作阶段 | 读取 current_chapter，从该章继续（写/改/确认） |
| `"review"` | 中断在全稿终审阶段 | 从终审重新启动 Reviewer |

读取 `chapter_status`：
- 如果 `current` 有值且 `pending_feedback` 不为 null → 上一轮审查有反馈未处理，先处理反馈
- 如果 `current` 有值且 `structure_review_round` > 0 → 当前章节在结构审查循环中
- 如果 `current` 有值且 `quality_review_round` > 0 → 当前章节在质量审查循环中

#### 恢复 Step 4：向用户报告恢复状态

```
📖 恢复写作进度

项目：novel-{书名}
阶段：{大纲/写作第X章/终审}
已完成：{X}/{N} 章
上次更新：{时间}

叙事方向：{writing-context.md 中的当前态势}
待处理：{pending_feedback 或"无"}

要从这里继续吗？
```

#### 恢复 Step 5：重建上下文后继续

向子 Agent 传入任务时，额外附上恢复上下文：

```
## 恢复上下文（会话中断后续写）
你正在续写一部之前中断的小说。以下是关键上下文：

### 叙事方向
{writing-context.md 中的叙事方向部分}

### 审查反馈汇总
{writing-context.md 中的审查反馈部分}

### 当前进度
- 已完成：第1-第X章
- 当前任务：{具体任务}
- 需要注意：{任何待处理的反馈或用户意见}

请保持与已有章节的风格和方向一致，不要偏离已确认的叙事方向。
```

**关键原则：恢复后绝不允许"重新构思"或"从头开始"，除非用户明确要求。**

#### 恢复失败处理

| 情况 | 处理 |
|------|------|
| session-state.json 不存在 | 通过扫描 chapters/ 和 story-bible/ 手动推断进度 |
| writing-context.md 不存在 | 从大纲和已有章节中推断叙事方向，写入该文件 |
| 章节文件有但 Story Bible 未更新 | 先补全 Story Bible 再继续 |
| 用户说"不记得写到哪了" | 读取已有章节，给出摘要让用户确认 |

### 优先级

```
用户意见 > Reviewer 审查 > Agent 自主判断 > 模板默认值
```

### 中文创作

- 全程中文，除非用户明确要求其他语言
- 不自作主张添加用户没要求的元素
- 敏感题材婉拒并建议调整方向
