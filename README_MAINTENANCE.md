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

Home 页面现在分成几个小节：

```text
Introduction
Paper Search
Finish Notes
Reading Queue
Topic Slides
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
