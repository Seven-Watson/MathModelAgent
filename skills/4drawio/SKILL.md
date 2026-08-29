---
name: 4drawio
description: "数学建模非数据型图示绘制阶段。根据 ANALYSIS_MODELING_REPORT.md、RESULTS_REPORT.md 和已有 figures/ 生成技术路线图、子问题求解流程图、模型结构图、数据处理流程图等 DrawIO 图，并导出论文可引用 PDF。"
allowed-tools: Bash(*), Read, Write, Edit, Grep, Glob, Agent, WebSearch, WebFetch
---

# DrawIO 非数据图示绘制

本 skill 承接 `3coding-visual`。它只负责论文中的**非数据型图示**，例如技术路线图、求解流程图、模型结构图、数据处理流程图、变量关系图、指标体系图等。

## 数学建模规范参考

如需领域判断，读取 `../_references/math_modeling_norms.md` 中的“图表与可视化”和“非数据图工具选择”小节。该文件只作为规范知识库，不要求为了凑数量生成额外图示。

## 阶段边界

- 本阶段负责：DrawIO 源文件、非数据图 PDF、图示生成记录。
- 本阶段不负责：折线图、柱状图、散点图、热力图、箱线图、雷达图等数据图。这些由 `3coding-visual` 生成。
- 本阶段不重跑模型、不修改 `code/`，不改写 `reports/RESULTS_REPORT.md` 的数值结论。

## 必须产出

在当前工作目录创建或更新：

```text
figures/
  fig_roadmap.drawio
  fig_roadmap.pdf
  fig_flow_q1.drawio
  fig_flow_q1.pdf
  ...
reports/DRAWIO_REPORT.md
```

如果某类图不需要生成，必须在 `reports/DRAWIO_REPORT.md` 中说明原因。竞赛论文通常至少需要一张 `fig_roadmap` 技术路线图。

读取这些文件的目的不是提取数据作图，而是理解论文方法、章节结构、子问题关系和已有图表，避免重复。

## 工作流程

### Step 1: 盘点已有图表和需求

先读取以下文件（存在则读取）：`reports/ANALYSIS_MODELING_REPORT.md`、`reports/RESULTS_REPORT.md`、`figures/` 目录列表。

然后从前序文档提取非数据图需求，输出一个清单：

```text
DRAWIO PLAN CHECKLIST:
[ ] fig_roadmap      技术路线图，放在问题重述/绪论
[ ] fig_flow_q1      问题一求解流程图
[ ] fig_flow_q2      问题二求解流程图
[ ] fig_flow_q3      问题三求解流程图
[ ] fig_pipeline     数据处理流程图
[ ] fig_model        模型结构/变量关系图
```

清单不是固定模板，要根据题目实际删减或增补。不要为了凑图生成无意义图示。

### Step 2: 设计信息架构并判定图类型

不要拿到内容后直接画方框。每张图在绘制前必须先明确：

- 一句话图示目的：读者看完后应理解什么；
- 推荐阅读顺序：从左到右、从上到下、中心向外、循环或分支；
- 输入、处理、判断、反馈和输出分别是什么；
- 哪些内容属于主干，哪些只是辅助说明；
- 是否存在并行、分支、汇合、循环或层级关系。

根据真实方法关系选择图式，不默认套用线性流程：

| 关系 | 默认图式 |
| --- | --- |
| 连续阶段 | 分栏阶段图或横向流水线 |
| 多模型并行比较 | 并行泳道后汇合 |
| 层级指标 | 树形或分层结构 |
| 模型组成 | 中心核心 + 模块环绕 |
| 检验后修正模型 | 带反馈箭头的闭环 |
| 多源输入形成结果 | 漏斗或证据汇合图 |
| 决策规则 | 分支决策树 |

图式必须服从分析报告中的真实顺序、统计关系和因果方向；不得为了构图好看而改写方法逻辑。

常见图示选择：

| 图类型 | 文件名建议 | 适用场景 |
| --- | --- | --- |
| 技术路线图 | `fig_roadmap` | 展示整体解题路线、章节逻辑、方法串联 |
| 子问题求解流程图 | `fig_flow_q1`, `fig_flow_q2` | 展示单个子问题的输入、判断、算法、输出 |
| 数据处理流程图 | `fig_pipeline` | 展示数据清洗、特征构造、建模输入 |
| 模型结构图 | `fig_model` | 展示模块关系、变量关系、模型层次 |
| 指标体系图 | `fig_index_system` | 展示目标层、准则层、指标层 |
| 决策树/规则图 | `fig_decision_tree` | 展示分类规则、设备选择、策略分支 |

不要用 DrawIO 画这些图：

- 结果对比柱状图
- 预测误差曲线
- 灵敏度曲线
- 相关性热力图
- 分布图和箱线图

### Step 3: GPT Image 生成视觉概念稿

每张拟生成的正式非数据图，在确定信息架构后，若当前环境提供内置 GPT Image，则默认调用一次生成视觉概念稿。GPT Image 是视觉架构师，只负责结构、构图和风格提案，不是正式论文图的渲染器。

提交给 GPT Image 的内容必须包含：

- 图示类型与一句话目的；
- 节点原始文字清单；
- 节点之间的准确关系；
- 推荐阅读顺序；
- 允许优化的视觉项目；
- 禁止新增或改变的科学内容；
- 用户提供的风格参考图及其用途（如有）。

允许 GPT Image 优化：

- 整体布局与节点分组；
- 信息层级和主次视觉焦点；
- 色彩系统与留白；
- 节点形状和箭头走向；
- 阶段条、泳道、分区框和反馈环。

必须拒绝任何包含下列变化的概念稿或建议：

- 新增、删除或改写方法步骤；
- 修改数字、缩写、公式或模型名称；
- 改变箭头的科学含义；
- 添加不存在的因果关系或反馈关系；
- 把装饰元素伪装成研究内容；
- 直接把生成式位图作为正式论文图。

概念稿保存到 `figures/design_references/`，使用无步骤编号的语义名称，例如：

```text
figures/design_references/fig_roadmap_concept.png
figures/design_references/fig_flow_q1_concept.png
```

该目录中的文件必须明确标记“视觉概念参考，非正式论文图”，并满足以下隔离规则：

- 不写入正式 PDF 图示清单；
- 不被论文写作阶段自动复制或引用；
- 不参与正式图数量统计；
- 不替代 `.drawio` 或 PDF。

GPT Image 不可用、生成失败或文字严重错误时，在 `reports/DRAWIO_REPORT.md` 记录原因并继续纯 DrawIO 流程，不得阻断本阶段。

### Step 4: 使用 DrawIO 重建正式图

每张图一个 `.drawio` 文件，放在 `figures/`。

GPT Image 概念稿只作为布局和风格参考。DrawIO 重建时必须重新使用 `reports/ANALYSIS_MODELING_REPORT.md` 和 `reports/RESULTS_REPORT.md` 中的原始文字与关系，不得从概念稿 OCR 复制、猜测或反推科学内容。

DrawIO 源文件和导出的 PDF 不使用步骤编号，保持 `fig_roadmap.drawio/.pdf`、`fig_flow_q1.drawio/.pdf` 等语义名称；不得生成编号副本。`reports/DRAWIO_REPORT.md` 同样保持标准名称，不添加编号。

DrawIO 内容要求：

- 文字语言与论文语言一致。
- 节点文字短，必要时双行，不堆长句。
- 同类节点样式统一。
- 箭头方向清晰，避免交叉。
- 图中不写大段解释，解释留给论文正文。
- 不使用装饰性阴影和过度渐变。
- 使用白色背景和克制的低饱和配色；红色只用于风险、否定、终止或关键强调。
- 箭头不得穿过节点；无法避免交叉时，优先调整布局，其次使用清晰的跳线。
- 图中不写大标题，标题交给论文 caption；最终插入论文的尺寸下文字必须可读。
- 每张正式图只保留一个权威 `.drawio` 和一个同名 PDF，不生成 `*_staged`、`*_final` 等并列正式版本。
- 替换旧图前保留原文件；只有用户明确要求删除时才移除旧版本。

生成大 XML 时，分段写入，避免截断。示例：

```bash
mkdir -p figures
cat << 'XMLEOF' > figures/fig_roadmap.drawio
<mxfile>
  <diagram name="Page-1">
    <mxGraphModel>
      <root>
        <mxCell id="0"/>
        <mxCell id="1" parent="0"/>
        <!-- nodes and edges -->
      </root>
    </mxGraphModel>
  </diagram>
</mxfile>
XMLEOF
```

### Step 5: 导出 PDF

优先用可用的 DrawIO 命令导出 PDF：

```bash
DRAWIO_BIN="$(command -v drawio 2>/dev/null || command -v draw.io 2>/dev/null || command -v draw.io.exe 2>/dev/null || true)"
if [ -n "$DRAWIO_BIN" ]; then
  "$DRAWIO_BIN" --export --format pdf --crop --output figures/fig_roadmap.pdf figures/fig_roadmap.drawio
else
  echo "DrawIO command not found; keep .drawio source and record export failure."
fi
```

如果无法导出 PDF，保留 `.drawio`，在 `reports/DRAWIO_REPORT.md` 记录失败原因和建议导出命令。

正式 PDF 必须由对应 DrawIO 源文件导出，不得把 GPT Image 概念 PNG 包装成 PDF 作为正式交付。

### Step 6: 自检和修复

每张图必须检查：

- `.drawio` 文件非空。
- 若导出成功，`.pdf` 文件非空。
- 节点没有明显重叠。
- 箭头不穿过核心节点。
- 字号、颜色、边框风格一致。
- 文件名和图意一致。
- 没有与 `3coding-visual` 的数据图重复。
- 图示文字、箭头和分组与 `.drawio` 源文件一致，PDF 确由该源文件导出。
- GPT Image 概念稿未进入正式图清单、论文图片目录或正式图数量统计。
- 在论文最终插入尺寸下，阅读顺序清楚，文字无裁切，颜色和线条能够区分。

发现问题要修 `.drawio` 并重新导出，不要只在报告里解释。

### Step 7: 写生成记录

创建 `reports/DRAWIO_REPORT.md`，至少包含：

```markdown
# DrawIO 图示生成报告

## 图示清单
| 文件 | 类型 | 来源依据 | 用途 | 状态 |
| --- | --- | --- | --- | --- |

## 未生成图示及原因

## 导出与自检记录

## GPT Image 概念设计记录
| 正式图 | 一句话目的 | 图式 | GPT Image 状态 | 概念稿 | 采纳项 | 拒绝项 | DrawIO 重建 | 最终验收 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

## 给论文阶段的嵌入建议
```

每张图的概念设计记录至少写明：图示目的、选用图式、GPT Image 是否成功、概念稿路径、采纳的布局或风格、拒绝的文字或科学关系变化、DrawIO 重建状态、PDF 导出和最终视觉检查结果。

嵌入建议只说明每张图适合放入哪个章节和建议 caption，不生成 `*_typst_includes.typ`。最终的图表插入代码（Typst 的 `#figure(image(...), caption: [...])` 或 LaTeX 的 `\begin{figure}...\end{figure}`）由 `5writing` 根据论文结构和所选引擎决定。

## 质量要求

- 图示服务论文论证，不为装饰而画。
- 每张图必须能对应到`reports/ANALYSIS_MODELING_REPORT.md` 中的真实方法。
- 数据型图表不得在本阶段重复生成。
- 论文阶段引用的非数据图都应有 `.drawio` 源文件和 PDF，或者在 `reports/DRAWIO_REPORT.md` 说明导出失败。
- GPT Image 概念稿只用于设计参考，不能作为论文正式图或科学证据。
- 旧项目没有 `figures/design_references/` 或 GPT Image 记录时仍然合法，不要求迁移、改名或重跑；原有 `.drawio`、PDF 和 `DRAWIO_REPORT.md` 接口继续有效。
