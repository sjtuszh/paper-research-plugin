---
name: paper-search
description: Unified paper search command. Intelligently determines whether the user needs survey/review papers, frontier research, or methods papers based on their query. Orchestrates multi-source search across Semantic Scholar, arXiv, CNKI, and ScienceDirect.
argument-hint: <topic-or-question>
---

# Paper Search — 智能论文检索（统一入口）

Intelligently determines the type of papers needed and searches across multiple sources.

## Arguments

$ARGUMENTS contains the user's research topic, question, or problem description.

## Analysis

Analyze the user's input to determine:

1. **What type of papers are needed?**
   - **Survey/Review needed** if: "综述", "survey", "review", "概述", "了解", "入门", "overview", "comprehensive", "get started"
   - **Frontier needed** if: "前沿", "最新", "最新进展", "cutting-edge", "recent", "state-of-the-art", "SOTA", "进展"
   - **Methods needed** if: user describes a specific problem, experiment, technique, "方法", "如何解决", "怎么实现", "effect", "performance issue", "baseline"
   - **All types** if: general exploration, "查一下", "找论文", "关于", "research on"

2. **Generate search keywords**: Extract core technical terms. If Chinese input, generate English translations.

3. **Choose search sources**:
   - Semantic Scholar API (always, for broad OA coverage)
   - arXiv API (always, for latest preprints)
   - CNKI skills (if available, for Chinese closed-access)
   - ScienceDirect skills (if available, for English closed-access)

## Execution

### Step 1: Search each source

For each paper type identified, invoke the corresponding search:

- Survey search: Use paper-survey's API calling strategy
- Frontier search: Use paper-frontier's API calling strategy
- Methods search: Use paper-methods' API calling strategy

### Step 2: Merge and deduplicate

Combine results from all sources, remove duplicates (by title similarity).

### Step 3: Evaluate and rank

For each unique paper, assess:
- Relevance to the original query (high/medium/low)
- Paper type match (is it really a survey/frontier/methods paper?)
- Quality signals (citations, venue, recency)

### Step 4: Present categorized results

```

## 📖 论文检索结果 — {topic}

### 📚 综述文献
共 X 篇
1. {Title} — {Authors} ({Year}) — ⭐高相关
   摘要: {TLDR}
   ...

### 🔬 前沿研究
共 Y 篇
...

### 🛠 可参考方法
共 Z 篇
...

### 💡 建议
- 如果想深入了解某篇论文，使用 /paper-fetch "{Title}"
- 如果想看特定类型，使用 /paper-survey, /paper-frontier, /paper-methods
```

## Notes

- For each category, list papers in order of relevance (high to low)
- Mark papers with ⭐ if they are highly recommended
- If the user's query is very specific (mentions concrete problem), prioritize methods papers
- If the query is broad/exploratory, prioritize survey papers
- Always mention whether a paper has open-access full text available
