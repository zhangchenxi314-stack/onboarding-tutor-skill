# onboarding-tutor — 业务上手导师

让 Hermes Agent 成为新人的业务导��，通过互动式问答帮助快速理解业务框架和流程。

---

## 目录

- [快速开始](#快速开始)
- [核心能力](#核心能力)
- [使用流程](#使用流程)
- [教学示例](#教学示例)
- [命令参考](#命令参考)
- [FAQ](#faq)

---

## 快速开始

### 1. 确认 Skill 已安装

```bash
hermes skills list | grep onboarding-tutor
```

预期输出：
```
│ onboarding-tutor        │ productivity         │ local   │ local   │ enabled │
```

### 2. 加载 Skill

在 Hermes 会话中输入：

```
/skill onboarding-tutor
```

或者启动时预加载：

```bash
hermes -s onboarding-tutor
```

### 3. 丢资料开始学习

直接把业务文档、流程图、README 等资料发给 Agent，它会自动进入教学流程。

---

## 核心能力

| 能力 | 说明 |
|------|------|
| 材料驱动的引导式学习 | 基于你提供的资料提炼要点，生成引导问题 |
| 可视化讲解 | 自动生成 Mermaid 时序图 + Markdown 表格 |
| 递进式知识结构 | 概念层 → 机制层 → 关联层 → 实践层，层层深入 |
| 故事化叙事 | 点→面→点，从具体场景切入建立全局观 |
| 客观评价 | 严格指出不足，不盲目鼓励 |
| 选择题测试 | 3-5 道选择题，覆盖概念/流程/场景 |
| 追问上限 | 同一主题最多 3 轮追问，避免死磕 |

---

## 使用流程

### 阶段一：丢资料 → 摸底

```
你：/skill onboarding-tutor
你：这是我们的订单系统设计文档 [丢资料]

Agent：
  我已读取订单系统设计文档，涉及以下模块：
  - 订单生命周期管理
  - 库存预占与扣减
  - 支付回调与履约触发

  在开始深入之前，先了解下你的情况：
  1. 你之前接触过分布式事务吗？
  2. 对消息队列（Kafka/RabbitMQ）有使用经验吗？
```

### 阶段二：引导提问

```
Agent：
  我从文档中提炼了 3 个核心要点，请你思考：

  1. 订单从创建到完成的完整状态流转链路是什么？
  2. 为什么库存采用"预占+确认"模式而不是直接扣减？
  3. 支付回调失败时，系统如何保证数据一致性？

  请试着回答，不需要完美，把你的理解说出来就行。
```

### 阶段三：深度讲解（配图配表）

```
你：（回答引导问题）

Agent：
  【时序图】订单创建完整链路：

  sequenceDiagram
      participant U as 用户
      participant G as API网关
      participant O as 订单服务
      ...（完整时序图）

  【分点讲解】
  1. 概念层：库存预占是什么
     ...

  2. 机制层：预占如何实现
     ...

  3. 关联层：和支付、履约的关系
     ...

  4. 实践层：大促场景下的注意事项
     ...
```

### 阶段四：选择题测试

```
Agent：
  来测试一下你的理解程度（3 道选择题）：

  1. 【单选】订单超时取消时，库存预占记录应如何处理？
     A. 保留，等待用户重新下单
     B. 自动释放，归还可用库存
     C. 标记为无效，人工处理
     D. 不做任何处理

  2. 【多选】以下哪些场景下需要回滚库存预占？（选择 2 项）
     ...

  3. 【场景】某次大促，监控显示订单创建耗时飙升，最先排查什么？
     ...

  请一次性提交答案，格式：1A 2BD 3C
```

### 阶段五：总结

```
Agent：
  测试结果：2/3 正确（67%）

  ✓ 第1题 正确
  ✗ 第2题 错误 — 你对"支付回调失败"场景的库存处理理解有偏差
  ✓ 第3题 正确

  知识点回顾：
  - 已掌握：订单状态机、排查思路
  - 薄弱点：支付异常场景下的库存一致性保障

  建议：重点回读文档第 3.2 节"异常处理"，画出支付回调的各种异常分支。

  下一主题预告：履约服务的异步处理机制。可以提前准备相关资料。
```

---

## 教学示例

### 时序图

Agent 会自动为流程相关的内容生成 Mermaid 时序图：

```
sequenceDiagram
    participant U as 用户
    participant O as 订单服务
    participant I as 库存服务
    participant P as 支付服务

    U->>O: 创建订单
    O->>I: 预占库存
    I-->>O: 预占成功
    O->>P: 发起支付
    P-->>O: 支付成功
    O->>I: 确认扣减库存
    I-->>O: 扣减成功
    O-->>U: 订单完成
```

在支持 Mermaid 的查看器中打开可渲染为可视化流程图（GitHub、Notion、Obsidian、VS Code 均支持）。

### 表格

对比类信息自动使用表格：

| 方案 | 实时性 | 一致性 | 复杂度 | 适用场景 |
|------|--------|--------|--------|----------|
| 同步扣减 | 高 | 强 | 低 | 低并发 |
| 预占+确认 | 中 | 最终一致 | 中 | 常规电商 |
| 异步队列 | 低 | 弱 | 高 | 高并发大促 |

---

## 命令参考

| 操作 | 命令 |
|------|------|
| 加载 Skill | `/skill onboarding-tutor` 或 `hermes -s onboarding-tutor` |
| 查看 Skill 详情 | `skill_view(name="onboarding-tutor")` |
| 查看时序图规范 | `skill_view(name="onboarding-tutor", file_path="references/sequence-diagram-guide.md")` |
| 查看测试模板 | `skill_view(name="onboarding-tutor", file_path="references/test-template.md")` |
| 退出教学模式 | `/reset`（开始新会话） |

---

## FAQ

### Q: Agent 怎么读取我的业务文档？

直接粘贴文本，或者告诉 Agent 文档的文件路径（在 Hermes 可访问的文件系统内）。支持 .md、.txt、.pdf（需 pymupdf）、.docx。

### Q: 时序图在终端里看不清怎么办？

Mermaid 代码块在终端显示为纯文本。可以复制到以下任一工具查看：
- VS Code（安装 Mermaid 插件）
- GitHub Markdown 预览
- Notion / Obsidian
- https://mermaid.live

### Q: 可以跳过某些阶段吗？

可以。直接告诉 Agent："跳过摸底，直接讲解"、"跳过测试，进入下一主题"、"我只想问一个问题"。

### Q: 为什么 Agent 不夸我？

这是设计如此。onboarding-tutor 采用客观严格评价，只指出对错和原因，不使用鼓励性语言。这有助于你准确了解自己的薄弱点。

### Q: 一个主题讲到一半可以换吗？

可以。直接说"这个话题先跳过，讲 XXX"。Agent 会保存当前进度，后续可以回来继续。

### Q: Agent 在一个问题上追问太多次怎么办？

追问上限为 3 轮。超过后 Agent 会自动标记为"待巩固"并推进到下一主题。你也可以主动说"这个问题先过，继续往下"。

### Q: Skill 文件在哪？

```
~/.hermes/skills/productivity/onboarding-tutor/
├── SKILL.md
└── references/
    ├── sequence-diagram-guide.md
    ├── table-format-guide.md
    ├── storytelling-pattern.md
    └── test-template.md
```

可以手动编辑 SKILL.md 调整教学行为（修改后需 `/reset` 生效）。

### Q: 如何关闭教学模式？

```
/reset
```

开始新会话即可退出。如果需要在新会话中继续之前的学习，使用 `/resume` 恢复。
