# PathOmics Notes 维护说明

这个文件是给自己后续维护网站用的。你可以把它理解为一个“操作手册”：以后新增论文、新增 topic、修改首页内容、调整目录时，主要看这份文档即可。

---

## 1. 项目文件结构

当前网站主要由 4 个核心文件维护：

```text
ReadingNotes/
├── index.html      # 网站页面结构：侧边栏、Home 各小节、页面容器
├── style.css       # 网站样式：颜色、布局、卡片、响应式适配
├── script.js       # 网站数据和交互：论文列表、topic 页面、搜索、切换页面
├── README.md       # GitHub 项目介绍
└── notes/          # 存放 PDF 阅读笔记
    ├── OmiCLIP.pdf
    ├── STORM.pdf
    ├── MultiEmbed.pdf
    └── ...
```

一般情况下：

- **新增一篇论文**：主要改 `script.js`
- **新增一个 topic**：需要同时改 `index.html` 和 `script.js`
- **调整页面样式**：改 `style.css`
- **修改 GitHub 主页介绍**：改 `README.md`
- **上传新的 PDF 笔记**：放到 `notes/` 文件夹

---

## 2. 最常见任务：添加一篇新论文

### 2.1 把 PDF 笔记放入 `notes/`

例如你新写了一篇阅读笔记：

```text
notes/RegVelo.pdf
```

建议文件名尽量简洁，不要有空格。推荐：

```text
RegVelo.pdf
MultiVeloVAE.pdf
scGPT.pdf
OmiCLIP.pdf
```

不太推荐：

```text
Wang et al. - 2024 - RegVelo gene-regulatory-informed dynamics of single cells.pdf
```

因为路径太长，网页链接更容易出错。

---

### 2.2 在 `script.js` 中添加论文对象

打开 `script.js`，找到类似这样的结构：

```js
paper('Nature Methods 2025', 'A Visual Omics Foundation Model to Bridge Histopathology with Spatial Transcriptomics', ['OmiCLIP', 'Histology', 'Spatial Transcriptomics'], 'A visual-omics foundation model linking tissue images with transcriptomic signals across organs.', 'https://www.nature.com/articles/s41592-025-02707-1', {
  question: 'Can histology images and spatial transcriptomes be aligned in a shared representation space?',
  method: 'Uses contrastive visual-omics pretraining over paired tissue patches and gene-expression sentences.',
  value: 'Central paper for image-guided spatial omics and Pathology Omics modeling.',
  caution: 'Prediction maps should be interpreted as model estimates rather than direct molecular measurements.',
  noteFile: 'notes/OmiCLIP.pdf',
  completed: true,
  completedDate: '2026-05-19',
  completedLabel: 'PDF note'
})
```

你可以复制这一段，然后改成新论文的信息。

例如新增 RegVelo：

```js
paper('Nature 2024', 'RegVelo: Gene-Regulatory-Informed Dynamics of Single Cells', ['RegVelo', 'RNA Velocity', 'Gene Regulation'], 'A gene-regulatory-informed model for learning single-cell dynamics and recovering lineage-specific regulatory programs.', 'https://www.nature.com/articles/xxxx', {
  question: 'Can gene regulatory priors improve the inference of single-cell state transitions?',
  method: 'Combines RNA velocity-style dynamic modeling with gene regulatory network information.',
  value: 'Useful for connecting cell-state dynamics with mechanistic regulatory interpretation.',
  caution: 'The inferred dynamics depend on regulatory prior quality and should be validated with perturbation evidence.',
  noteFile: 'notes/RegVelo.pdf',
  completed: true,
  completedDate: '2026-05-29',
  completedLabel: 'PDF note'
})
```

字段解释：

| 字段 | 作用 |
|---|---|
| `venue` | 期刊/会议/年份，例如 `Nature 2024` |
| `title` | 论文标题 |
| `tags` | 标签，会显示在卡片上 |
| `excerpt` | 首页或 topic 页面中的简短介绍 |
| `url` | 论文 DOI、Nature 链接、arXiv 链接或项目主页 |
| `question` | 这篇文章解决什么问题 |
| `method` | 方法核心思想 |
| `value` | 为什么值得读 |
| `caution` | 阅读时要注意的局限 |
| `noteFile` | 本地 PDF 笔记路径 |
| `completed` | 是否已经读完 |
| `completedDate` | 完成日期 |
| `completedLabel` | 显示标签，例如 `PDF note` |

---

### 2.3 把论文放进对应 topic

在 `script.js` 中，每个 topic 通常长这样：

```js
'cbio-dynamics': {
  family: 'Computational Biology',
  tone: 'bio',
  title: 'Cell State Dynamics',
  subtitle: 'Trajectory inference, latent time, velocity, fate landscapes, and temporal modeling from static or time-resolved single cell data.',
  note: 'For dynamic models, the reading note should distinguish ordering from velocity, and both from actual causal transition.',
  rows: [
    ['Latent time', 'Infer ordered biological progression from heterogeneous cell states.', null],
    ['Velocity models', 'Estimate transition directions using RNA, chromatin, or multimodal kinetics.', null],
    ['Fate landscapes', 'Model branching, terminal states, and lineage-specific regulatory programs.', null]
  ],
  papers: [
    // 把新论文放在这里
  ]
}
```

如果 RegVelo 属于 RNA velocity / cell dynamics，就放到：

```js
'cbio-dynamics'
```

也可以放到 regulatory network：

```js
'cbio-regulatory'
```

如果一篇文章属于多个 topic，最简单的做法是**复制同一个 paper 对象到多个 topic 的 `papers` 列表里**。这样维护成本最低。

---


---

# 新增：完成一篇新笔记后的标准维护流程

这一节是最常用的操作说明。以后每完成一篇新笔记，可以直接按照下面的顺序检查和修改。

## A. 一句话版本

完成一篇新笔记后，一般需要做三件事：

```text
1. 把 PDF 笔记放到 notes/ 文件夹
2. 在 script.js 中把论文加入对应 topic 的 papers 列表
3. 在 script.js 中把这篇笔记加入 const finishedNotes = [...]
```

如果这篇论文原来已经在 topic 里，只是现在补充了 PDF 笔记，那么通常只需要：

```text
1. 给原来的 paper(...) 对象补充 noteFile
2. 在 finishedNotes 中添加一条记录
```

---

## B. 推荐文件命名规则

推荐使用简洁英文文件名：

```text
notes/CellRank.pdf
notes/CellRank2.pdf
notes/RegVelo.pdf
notes/MultiVeloVAE.pdf
notes/OmiCLIP.pdf
```

不推荐使用：

```text
notes/Cell Rank2。pdf
notes/Wang et al. - 2024 - RegVelo gene-regulatory-informed dynamics of single cells.pdf
notes/王等-2024-RegVelo.pdf
```

原因：

```text
1. 空格、中文标点、括号可能导致 GitHub Pages 链接不稳定
2. 路径太长，不方便维护
3. macOS 本地大小写不敏感，但 GitHub Pages 大小写敏感
```

特别注意：

```text
CellRank2.pdf
CellRank2。pdf
```

这两个不是同一个文件名。第二个里面是中文句号，网页链接很容易出问题。

---

## C. 把新笔记加入对应分类

### C.1 找到对应 topic

在 `script.js` 中搜索 topic key，例如：

```js
'cbio-velocity'
```

常见分类对应关系：

| 方向 | topic key | 适合放的论文 |
|---|---|---|
| Multi-omics Integration | `cbio-integration` | GLUE, MultiVI, MIDAS, totalVI, MOFA+, LIGER |
| RNA Velocity & Cell Dynamics | `cbio-velocity` | scVelo, CellRank, CellRank 2, MultiVelo, RegVelo |
| Cell Foundation Models | `cbio-foundation` | scGPT, Geneformer, Nicheformer, scPRINT |
| Gene Regulatory Networks | `cbio-regulatory` | SCENIC, CellOracle, RegVelo |
| Perturbation Prediction | `cbio-perturbation` | GEARS, scGen, CPA, CellOT |
| Spatial Omics | `cbio-spatial` | Tangram, cell2location, STAGATE, Nicheformer |
| Pathology Foundation Models | `path-foundation` | UNI, Virchow, CHIEF, GigaPath |
| Pathology Omics | `bridge-histology` / `bridge-visualomics` / `bridge-molecular` | OmiCLIP, STORM, PORPOISE, Multi-Embed |

---

### C.2 在 topic 的 `papers` 列表中添加论文

例如你完成了 CellRank 2 笔记，希望加入 RNA Velocity & Cell Dynamics：

```js
paper('Nature Methods 2024', 'CellRank 2: Unified Fate Mapping in Multiview Single-cell Data', ['CellRank 2', 'Fate Mapping', 'Multiview'], 'A unified and scalable fate-mapping framework that integrates multiple directional views such as pseudotime, RNA velocity, experimental time and metabolic labeling.', 'https://www.nature.com/articles/s41592-024-02303-9', {
  question: 'How can fate mapping combine multiple directional signals instead of relying on RNA velocity alone?',
  method: 'Combines multiple kernels in a unified Markov-state modeling framework to infer terminal states and fate probabilities.',
  value: 'Important for understanding modern multiview cell-state dynamics and fate prediction.',
  caution: 'The chosen view or kernel can strongly shape inferred fate probabilities and should be validated biologically.',
  noteFile: 'notes/CellRank2.pdf',
  completed: true,
  completedDate: '2026-06-03',
  completedLabel: 'PDF note'
})
```

如果后面还有别的论文，记得加逗号：

```js
paper(...),
paper(...)
```

---

## D. 把新笔记加入 Finish Notes

Finish Notes 由 `script.js` 里的这个数组控制：

```js
const finishedNotes = [
  ...
];
```

它不会自动从 topic 里抓取，所以必须手动加一条。

### D.1 Finish Notes 标准模板

```js
{
  date: '2026-06-03',
  title: 'CellRank 2: Unified Fate Mapping in Multiview Single-cell Data',
  venue: 'Nature Methods 2024',
  desc: 'Multiview fate mapping with velocity, pseudotime, experimental time and metabolic labeling.',
  href: 'notes/CellRank2.pdf',
  tags: ['CellRank 2', 'Multiview', 'Cell Dynamics'],
  tone: 'bio'
}
```

字段说明：

| 字段 | 作用 |
|---|---|
| `date` | 完成笔记的日期，建议用 `YYYY-MM-DD` |
| `title` | Finish Notes 中显示的标题 |
| `venue` | 期刊/会议/年份 |
| `desc` | 一句话概括这篇笔记 |
| `href` | PDF 或 HTML 笔记路径 |
| `tags` | Finish Notes 中显示的标签 |
| `tone` | 页面颜色风格：`bio` / `path` / `bridge` |

`tone` 推荐规则：

```text
bio     Computational Biology
path    Computational Pathology
bridge  Pathology Omics
```

---

### D.2 新完成的笔记放在哪里？

建议放在 `finishedNotes` 数组最前面，这样页面会显示在最上方：

```js
const finishedNotes = [
  {
    date: '2026-06-03',
    title: 'New Finished Paper',
    ...
  },
  {
    date: '2026-05-19',
    title: 'Old Finished Paper',
    ...
  }
];
```

---

## E. 如果一篇文章属于多个分类怎么办？

有两种做法。

### 做法 1：直接复制同一个 `paper(...)`

适合偶尔交叉的文章，最简单。

例如 RegVelo 可以同时放在：

```text
cbio-velocity
cbio-regulatory
```

### 做法 2：定义一个常量，然后多处复用

适合很重要、以后可能多处引用的文章。

```js
const CELLRANK2_PAPER = paper('Nature Methods 2024', 'CellRank 2: Unified Fate Mapping in Multiview Single-cell Data', ['CellRank 2', 'Fate Mapping', 'Multiview'], 'A unified and scalable fate-mapping framework for multiview single-cell fate mapping.', 'https://www.nature.com/articles/s41592-024-02303-9', {
  question: 'How can fate mapping combine multiple directional signals?',
  method: 'Combines multiple kernels in a unified Markov-state framework.',
  value: 'Important for modern multiview cell-state dynamics.',
  caution: 'Kernel choice can strongly affect inferred fate probabilities.',
  noteFile: 'notes/CellRank2.pdf',
  completed: true,
  completedDate: '2026-06-03',
  completedLabel: 'PDF note'
});
```

然后在 topic 里写：

```js
papers: [
  CELLRANK2_PAPER,
  ...
]
```

这样以后修改标题、链接、笔记路径时，只需要改常量定义。

---

## F. 完整案例：添加 CellRank 和 CellRank 2

### F.1 在 `script.js` 顶部定义常量

```js
const CELLRANK_PAPER = paper('Nature Methods 2022', 'CellRank for Directed Single-cell Fate Mapping', ['CellRank', 'Fate Mapping', 'Markov Model'], 'A directed single-cell fate-mapping framework that combines RNA velocity-informed transition kernels with Markov state modeling to identify terminal states and compute fate probabilities.', 'https://www.nature.com/articles/s41592-021-01346-6', {
  question: 'How can transition directions be converted into terminal states and probabilistic cell-fate decisions?',
  method: 'Builds transition kernels on a cell-cell graph and uses Markov modeling to estimate terminal states and fate probabilities.',
  value: 'A core paper for moving from RNA velocity vectors to interpretable fate mapping.',
  caution: 'Terminal-state identification, root-state choice, and kernel design require biological checking.',
  noteFile: 'notes/CellRank.pdf',
  completed: true,
  completedDate: '2026-06-03',
  completedLabel: 'PDF note'
});

const CELLRANK2_PAPER = paper('Nature Methods 2024', 'CellRank 2: Unified Fate Mapping in Multiview Single-cell Data', ['CellRank 2', 'Fate Mapping', 'Multiview'], 'A unified and scalable fate-mapping framework that integrates multiple directional views such as pseudotime, RNA velocity, experimental time and metabolic labeling.', 'https://www.nature.com/articles/s41592-024-02303-9', {
  question: 'How can fate mapping combine multiple directional signals instead of relying on RNA velocity alone?',
  method: 'Combines multiple kernels in a unified Markov-state modeling framework to infer terminal states and fate probabilities.',
  value: 'Important for understanding modern multiview cell-state dynamics and fate prediction.',
  caution: 'The chosen view or kernel can strongly shape inferred fate probabilities and should be validated biologically.',
  noteFile: 'notes/CellRank2.pdf',
  completed: true,
  completedDate: '2026-06-03',
  completedLabel: 'PDF note'
});
```

### F.2 在 `cbio-velocity` 的 papers 里加入

```js
papers: [
  CELLRANK_PAPER,
  CELLRANK2_PAPER,
  ...
]
```

### F.3 在 `finishedNotes` 里加入

```js
{
  date: '2026-06-03',
  title: 'CellRank for Directed Single-cell Fate Mapping',
  venue: 'Nature Methods 2022',
  desc: 'Directed Markov modeling for terminal states and probabilistic cell-fate decisions.',
  href: 'notes/CellRank.pdf',
  tags: ['CellRank', 'Fate Mapping', 'RNA Velocity'],
  tone: 'bio'
},
{
  date: '2026-06-03',
  title: 'CellRank 2: Unified Fate Mapping in Multiview Single-cell Data',
  venue: 'Nature Methods 2024',
  desc: 'Multiview fate mapping with velocity, pseudotime, experimental time and metabolic labeling.',
  href: 'notes/CellRank2.pdf',
  tags: ['CellRank 2', 'Multiview', 'Cell Dynamics'],
  tone: 'bio'
}
```

---

## G. 添加 Reading Queue

Reading Queue 适合放“准备读但还没读完”的论文。

在 `script.js` 中找到：

```js
const readingQueue = [
  ...
];
```

添加：

```js
{
  venue: 'Nature Methods 2025',
  title: 'Paper Title',
  tags: ['Spatial Omics', 'To Read'],
  url: 'https://paper-link.com',
  noteFile: '',
  topicTitle: 'Spatial Omics',
  family: 'Computational Biology',
  tone: 'bio'
}
```

等读完后，再做两件事：

```text
1. 把它从 Reading Queue 移除，或者保留但补上 noteFile
2. 把它加入 finishedNotes
```

---

## H. 添加 New Papers

New Papers 适合放“最新值得关注，但不一定已经读完”的论文。

在 `script.js` 中找到：

```js
const recentAcceptedPapers = [
  ...
];
```

添加：

```js
{
  venue: 'Nature Methods 2026',
  title: 'New Paper Title',
  tags: ['New', 'Spatial Omics', 'Method'],
  url: 'https://paper-link.com',
  noteFile: '',
  topicTitle: 'RNA Velocity & Cell Dynamics',
  family: 'Computational Biology',
  tone: 'bio'
}
```

建议：

```text
1. New Papers 不一定要有 PDF note
2. 如果后面读完了，再加入 finishedNotes
3. 如果它属于某个成熟方向，也建议同步加入对应 topic
```

---

## I. 添加 Topic Slides

Topic Slides 适合放“一个方向的综合笔记”，例如：

```text
Multi-omics Integration
RNA Velocity & Cell Dynamics
Pathology Foundation Models
```

在 `script.js` 中找到：

```js
const topicSummaryNotes = [
  ...
];
```

添加：

```js
{
  title: 'RNA Velocity & Cell Dynamics',
  desc: 'A synthesis note for scVelo, UniTVelo, MultiVelo, CellRank, RegVelo, MultiVeloVAE, STORIES, and neural differential equation models.',
  meta: 'Computational Biology · Dynamics',
  href: 'notes_V1/Summary/RNAVelocityDynamics.html',
  tone: 'bio'
}
```

如果是 PDF：

```js
href: 'notes/RNAVelocityDynamics.pdf'
```

---

## J. 修改完成后的检查清单

### J.1 新增完成笔记后检查

```text
1. PDF 文件是否已经放入 notes/？
2. noteFile / href 路径是否和真实文件名完全一致？
3. 论文是否出现在正确 topic？
4. Finish Notes 是否出现？
5. 点击 Paper 是否打开原文？
6. 点击 Note 是否打开 PDF？
7. 搜索标题、作者或 tag 是否能搜到？
8. 浏览器 Console 是否没有红色报错？
```

### J.2 新增 topic 后检查

```text
1. 侧边栏是否有入口？
2. data-page 是否和 topic key 完全一致？
3. 是否有对应 <section class="page" id="page-xxx"></section>？
4. script.js 中 topics 是否有同名 key？
5. 页面能否正常打开？
6. 是否影响其他页面切换？
```

### J.3 修改样式后检查

```text
1. Light mode 是否正常？
2. Night mode 是否正常？
3. 窄屏幕/小窗口是否正常？
4. 侧边栏是否仍然可读？
5. 卡片 hover、按钮点击是否正常？
```

---

## K. JavaScript 语法检查

改完 `script.js` 后，最好运行：

```bash
node --check script.js
```

如果没有输出，通常说明语法没有问题。

常见报错原因：

```text
1. 少了逗号
2. 多了括号
3. 字符串引号没有闭合
4. 在数组或对象里写错了结构
```

常见正确格式：

```js
papers: [
  paper(...),
  paper(...),
  paper(...)
]
```

常见错误格式：

```js
papers: [
  paper(...)
  paper(...)
]
```

中间少了逗号。

---

## L. 推荐 commit 信息

```bash
git commit -m "add CellRank reading notes"
git commit -m "update RNA velocity reading list"
git commit -m "add new topic summary"
git commit -m "refine home page layout"
git commit -m "fix note links"
```


## 3. 当前推荐的 Computational Biology 目录

目前建议把计算生物部分整理成这些方向：

```text
Computational Biology
├── Multi-omics Integration
├── RNA Velocity & Cell Dynamics
├── Cell Foundation Models
├── Single-cell Agents
├── Regulatory Networks
├── Causal Inference
├── Perturbation Prediction
├── Spatial Omics
├── 3D Cell Atlases
├── Representation Learning
└── Pathology and Omics
```

每个方向适合放的文章类型如下。

### 3.1 Multi-omics Integration

适合放：

- 单细胞多组学整合
- RNA + ATAC
- RNA + protein
- mosaic integration
- cross-modal prediction
- missing modality imputation

代表文章：

```text
GLUE
MultiVI
MIDAS
Cobolt
totalVI
scMVP
```

---

### 3.2 RNA Velocity & Cell Dynamics

适合放：

- RNA velocity
- chromatin velocity
- multimodal velocity
- trajectory inference
- latent time
- fate prediction
- state transition modeling

代表文章：

```text
scVelo
MultiVelo
MultiVeloVAE
GraphVelo
RegVelo
UniTVelo
DeepVelo
```

---

### 3.3 Cell Foundation Models

适合放：

- 单细胞 foundation model
- gene expression transformer
- cell embedding pretraining
- atlas transfer
- universal cell representation

代表文章：

```text
scGPT
Geneformer
scFoundation
Nicheformer
scPRINT
UCE
```

---

### 3.4 Single-cell Agents

适合放：

- 用 LLM/agent 做单细胞分析
- 自动化生信分析流程
- 自动选择工具
- 自动解释细胞类型
- agentic biological discovery

代表方向：

```text
single-cell analysis agent
bioinformatics agent
LLM-based cell annotation
automated omics workflow
```

这个方向目前还比较新，文章不多。可以先作为未来扩展目录保留。

---

### 3.5 Regulatory Networks

适合放：

- gene regulatory network
- transcription factor activity
- enhancer-gene linking
- regulatory program discovery
- perturbation-based GRN inference

代表文章：

```text
CellOracle
SCENIC
RegVelo
GLUE regulatory graph
Geneformer network biology
```

---

### 3.6 Causal Inference

适合放：

- 因果推断
- causal representation learning
- counterfactual prediction
- intervention modeling
- causal discovery from single-cell data

代表方向：

```text
causal perturbation modeling
counterfactual cell-state prediction
causal gene regulatory inference
```

这个方向和 perturbation 很接近，但更强调“干预”和“反事实”。

---

### 3.7 Perturbation Prediction

适合放：

- CRISPR perturb-seq
- drug response prediction
- gene knockout prediction
- combinatorial perturbation
- unseen perturbation generalization

代表文章：

```text
GEARS
scGen
CPA
CellOT
PerturbNet
Geneformer perturbation tasks
```

---

### 3.8 Spatial Omics

适合放：

- spatial transcriptomics
- spatial proteomics
- spatial domain detection
- cell-cell communication
- tissue niche modeling
- image-guided spatial omics

代表文章：

```text
Tangram
cell2location
SpaGCN
STAGATE
Nicheformer
OmiCLIP
STORM
```

---

### 3.9 3D Cell Atlases

适合放：

- 3D tissue reconstruction
- 3D spatial cell atlas
- embryo atlas
- organ-scale spatial atlas
- spatial-temporal atlas

代表方向：

```text
3D cell atlas
whole-organ spatial atlas
developmental atlas
spatial-temporal reconstruction
```

---

### 3.10 Representation Learning

适合放：

- self-supervised learning
- contrastive learning
- graph representation learning
- variational autoencoder
- disentangled representation
- biological embedding evaluation

代表方向：

```text
contrastive single-cell learning
graph neural networks for omics
VAE-based omics embedding
biological representation benchmark
```

---

### 3.11 Pathology and Omics

适合放：

- pathology image + omics
- histology + spatial transcriptomics
- molecular prediction from H&E
- image-omics biomarker
- pathology foundation model linked to molecular data

代表文章：

```text
PORPOISE
OmiCLIP
STORM
Multi-Embed
HE2RNA
HisToGene
BLEEP
```

这个方向可以和网站里的 `Pathology Omics` 大类有交叉。如果文章更偏“计算生物问题”，放在 Computational Biology 里的 `Pathology and Omics`；如果更偏“病理图像/临床诊断”，放在 Pathology Omics 大类。

---

## 4. 如何新增一个 topic

新增 topic 需要改两个地方：

```text
index.html  # 侧边栏加入口
script.js   # 加 topic 数据
```

---

### 4.1 在 `index.html` 的侧边栏加入口

找到 Computational Biology 的导航部分，大概是：

```html
<details class="nav-group nav-bio" open>
  <summary class="nav-group-title">
    <span class="nav-icon folder-icon" aria-hidden="true"></span>
    <span class="nav-number">01</span>
    <span class="nav-text">Computational Biology</span>
  </summary>
  <div class="nav-links">
    ...
  </div>
</details>
```

在 `<div class="nav-links">` 里面添加一行：

```html
<a href="#cbio-new-topic" class="nav-link nav-item" data-page="cbio-new-topic">
  <span class="nav-icon doc-icon" aria-hidden="true"></span>
  <span class="nav-number">1.12</span>
  <span class="nav-text">New Topic Name</span>
</a>
```

注意：

- `href="#cbio-new-topic"` 要和 `data-page="cbio-new-topic"` 一致
- 这个 id 后面要在 `script.js` 中使用
- 编号 `1.12` 可以根据目录顺序自己调整

---

### 4.2 在 `script.js` 中添加 topic 数据

在 `const topics = { ... }` 里面添加：

```js
'cbio-new-topic': {
  family: 'Computational Biology',
  tone: 'bio',
  title: 'New Topic Name',
  subtitle: 'One-sentence description of this topic.',
  note: 'A short explanation of why this topic is important and how to read papers in this section.',
  rows: [
    ['Sub-question 1', 'What should be checked when reading this type of paper?', null],
    ['Sub-question 2', 'Another reading dimension.', null],
    ['Sub-question 3', 'Another reading dimension.', null]
  ],
  papers: [
    paper('Venue Year', 'Paper Title', ['Tag1', 'Tag2'], 'Short paper summary.', 'https://paper-link.com', {
      question: 'What problem does this paper solve?',
      method: 'What is the core method?',
      value: 'Why is it useful?',
      caution: 'What should be interpreted carefully?',
      noteFile: 'notes/example.pdf',
      completed: true,
      completedDate: '2026-05-29',
      completedLabel: 'PDF note'
    })
  ]
}
```

常见 `tone`：

```js
tone: 'bio'     // Computational Biology 蓝色风格
tone: 'path'    // Computational Pathology 棕色风格
tone: 'bridge'  // Pathology Omics 绿色风格
```

---

## 5. 如何修改 Home 页面

Home 页面现在分成几个单独页面：

```text
0.1 Introduction
0.2 Paper Search
0.3 New Papers
0.4 Finish Notes
0.5 Reading Queue
0.6 Topic Slides
```

它们主要在 `index.html` 中定义。

如果只是修改 Home 的文字、卡片、按钮，一般只改 `index.html`。

如果要修改 Home 中自动生成的文章列表，比如 Finish Notes 或 Reading Queue，则通常改 `script.js` 中的论文数据。

---

## 6. 如何控制一篇文章是否出现在 Finish Notes

一篇文章是否出现在 Finish Notes，通常取决于它的 `note` 字段里有没有这些信息：

```js
completed: true,
completedDate: '2026-05-29',
completedLabel: 'PDF note',
noteFile: 'notes/RegVelo.pdf'
```

如果你还没读完，可以写：

```js
completed: false
```

或者暂时不写这些字段。

---

## 7. 如何添加 Reading Queue

Reading Queue 通常适合放“准备读但还没读完”的论文。

建议在 `script.js` 中建立或修改类似这样的列表：

```js
const readingQueue = [
  paper('Nature Methods 2025', 'Paper Title', ['Spatial Omics'], 'Short description.', 'https://paper-link.com', {
    question: 'Why should I read this paper?',
    method: 'Expected method focus.',
    value: 'How it may help my research.',
    caution: 'What to check carefully.'
  })
];
```

如果你的网站当前已经有 `readingQueue`，直接往里面加就行。

---

## 8. 修改样式时应该改哪里

所有样式都在 `style.css`。

常见修改位置：

| 想修改的内容 | 搜索关键词 |
|---|---|
| 首页 Introduction | `home-intro` / `intro` / `hero` |
| 卡片样式 | `card` |
| 论文卡片 | `paper-card` |
| 侧边栏 | `sidebar` / `nav-link` |
| 主题颜色 | `--bio` / `--path` / `--bridge` / `--accent` |
| 深色模式 | `html[data-theme="dark"]` |
| 手机端布局 | `@media` |

如果你不确定改哪一段，最安全的方法是：

1. 先在 CSS 里搜索相关 class 名。
2. 只改一小处。
3. 本地刷新网页检查效果。
4. 没问题再继续改下一处。

---

## 9. 推荐的维护习惯

### 9.1 每次只做一种修改

不要一次同时改很多东西。推荐顺序：

```text
第 1 次 commit：新增 PDF 文件
第 2 次 commit：新增 paper 数据
第 3 次 commit：调整 topic 目录
第 4 次 commit：修改样式
```

这样出错时比较容易回退。

---

### 9.2 新增文章时先检查 4 件事

每次新增论文后，检查：

```text
1. PDF 文件能不能打开？
2. 论文卡片是否出现在正确 topic？
3. Finish Notes 是否出现？
4. 搜索能不能搜到标题或 tag？
```

---

### 9.3 文件名不要使用中文、空格和奇怪符号

推荐：

```text
notes/RegVelo.pdf
notes/MultiVeloVAE.pdf
notes/OmiCLIP.pdf
```

避免：

```text
notes/RegVelo gene-regulatory-informed dynamics of single cells (final version).pdf
notes/王等-2024-RegVelo.pdf
```

---

### 9.4 修改前先备份

如果你担心改坏，可以先复制一份：

```bash
cp index.html index_backup.html
cp script.js script_backup.js
cp style.css style_backup.css
```

或者用 Git：

```bash
git status
git add .
git commit -m "backup before adding new notes"
```

---

## 10. 本地预览网站

在项目根目录运行：

```bash
python -m http.server 8000
```

然后浏览器打开：

```text
http://localhost:8000
```

如果你只是双击 `index.html`，有时 PDF 路径或脚本行为可能不稳定，所以更推荐用本地服务器预览。

---

## 11. 提交到 GitHub

常用流程：

```bash
git status
git add .
git commit -m "add new reading note"
git pull --rebase origin main
git push origin main
```

如果你的网站是 GitHub Pages，push 之后通常需要等几十秒到几分钟才会更新。

---

## 12. 常见问题

### 12.1 新增论文后网页没有变化

可能原因：

```text
1. 忘记保存 script.js
2. 论文没有放进任何 topic 的 papers 列表
3. JavaScript 语法错误，例如少了逗号
4. 浏览器缓存没有刷新
```

解决方法：

```text
1. 按 Cmd + Shift + R 强制刷新
2. 打开浏览器开发者工具 Console 看有没有红色报错
3. 检查新增 paper 前后有没有逗号
```

---

### 12.2 页面突然空白

大概率是 `script.js` 有语法错误。

重点检查：

```js
papers: [
  paper(...),
  paper(...),
]
```

常见错误：

```js
paper(...) paper(...)
```

中间少了逗号。应该是：

```js
paper(...),
paper(...)
```

---

### 12.3 点击侧边栏没有反应

检查 `index.html` 中的：

```html
data-page="cbio-new-topic"
```

是否和 `script.js` 中的：

```js
'cbio-new-topic': {
```

完全一致。

大小写、横线、下划线都要一致。

---

### 12.4 PDF 打不开

检查：

```js
noteFile: 'notes/RegVelo.pdf'
```

是否和真实文件路径一致。

注意：

```text
RegVelo.pdf
regvelo.pdf
```

在 GitHub Pages 上可能被认为是两个不同文件名。大小写要完全一致。

---

## 13. 推荐的新论文添加模板

以后新增文章时，可以直接复制这个模板：

```js
paper('Venue Year', 'Paper Title', ['Tag1', 'Tag2', 'Tag3'], 'One-sentence summary of the paper.', 'https://paper-link.com', {
  question: 'What scientific or methodological question does this paper address?',
  method: 'What is the core method or model design?',
  value: 'Why is this paper useful for my research map?',
  caution: 'What limitation, assumption, or evaluation detail should I check carefully?',
  noteFile: 'notes/PaperShortName.pdf',
  completed: true,
  completedDate: '2026-05-29',
  completedLabel: 'PDF note'
})
```

---

## 14. 推荐的 topic 添加模板

以后新增 topic 时，可以复制这个模板：

```js
'cbio-topic-id': {
  family: 'Computational Biology',
  tone: 'bio',
  title: 'Topic Name',
  subtitle: 'Short description of this topic.',
  note: 'How I should read papers in this topic and what research questions matter most.',
  rows: [
    ['Reading axis 1', 'Description of the first reading axis.', null],
    ['Reading axis 2', 'Description of the second reading axis.', null],
    ['Reading axis 3', 'Description of the third reading axis.', null]
  ],
  papers: [
    // Add paper(...) objects here
  ]
}
```

---

## 15. 给未来自己的建议

这个网站不是一次性做完的，而是一个长期维护的知识地图。后续不需要每次都追求结构完全完美。更重要的是保持三个习惯：

1. 每读完一篇文章，就留下一个结构化卡片。
2. 每隔一段时间，把 topic 重新整理一次。
3. 每个 topic 不只堆论文，也写清楚这个方向真正关心的问题。

当论文越来越多时，这个网站的价值不只是“展示读过什么”，而是帮助你形成自己的研究地图。
