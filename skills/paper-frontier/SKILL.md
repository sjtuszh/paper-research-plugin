---
name: paper-frontier
description: Search for cutting-edge / frontier research papers on a given topic. Use when the user wants the latest advances, recent breakthroughs, or state-of-the-art developments.
argument-hint: <research-topic>
---

# Paper Frontier — 前沿文献检索

Search for the most recent cutting-edge research papers on a given topic. Returns recent publications with novelty assessment.

## Arguments

$ARGUMENTS contains the research topic (Chinese or English).

## Workflow

### 1. Search Semantic Scholar for recent papers

Call the Semantic Scholar API sorted by publication date (recent first):

```
https://api.semanticscholar.org/graph/v1/paper/search?query={topic}&limit=10&fields=title,authors,year,abstract,citationCount,venue,externalIds,publicationDate,tldr&sort=publicationDate
```

Focus on papers from 2025-2026 (current year range).

### 2. Search arXiv by submission date

```
https://export.arxiv.org/api/query?search_query=all:{topic}&start=0&max_results=15&sortBy=submittedDate&sortOrder=descending
```

### 3. If user's topic is in Chinese, also try English translation

### 4. Evaluate & Filter

For each paper:
- **Novelty**: Does it propose something new or just apply existing methods?
- **Recency**: Published in the last 1-2 years
- **Venue**: Is it from a top conference/journal?
- **Impact**: Citation count relative to age (citation velocity)
- **Relevance**: Directly addresses the user's topic

### 5. Present Results

```
## 🔬 前沿文献 — {topic}

共找到 N 篇前沿论文（Semantic Scholar: X, arXiv: Y）:

### ⭐ 重点关注
1. **{Title}** ({Year})
   作者: {Authors} | 出处: {Venue} | 引用: {citationCount}
   摘要: {Abstract / TLDR}
   🔑 创新点: {核心贡献/与已有工作的区别}

### 📌 其他相关
...

### 📊 趋势观察
- {观察到的研究方向趋势}
- {热门子方向}
```

## Notes

- For arXiv, sortBy=submittedDate gives the newest preprints first
- For Semantic Scholar, some recently published papers may have 0 citations — that's normal
- Distinguish between preprints (arXiv) and peer-reviewed publications
- Highlight papers from top venues (NeurIPS, ICML, ICLR, CVPR, ACL, Nature, Science, etc.)
- For Chinese topics, try both Chinese keywords and English translations
