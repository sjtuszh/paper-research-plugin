---
name: paper-methods
description: Search for directly applicable methods papers. Use when the user describes a specific research problem, experiment, or implementation challenge that needs a referenced method/solution.
argument-hint: <problem-description>
---

# Paper Methods — 方法文献检索

Search for papers that provide directly applicable methods, techniques, or solutions for a specific research problem.

## Arguments

$ARGUMENTS contains the problem description — the more specific, the better.

## Workflow

### 1. Extract Technical Keywords

From the user's problem description, extract specific technical terms. For example:
- "做 LLM 推理加速，用 Speculative Decoding" → keywords: "speculative decoding", "LLM inference acceleration", "draft model"
- "做知识图谱嵌入，效果不好" → keywords: "knowledge graph embedding", "KGE"

### 2. Search Semantic Scholar

Search with specific technical keywords:

```
https://api.semanticscholar.org/graph/v1/paper/search?query={technical-keywords}&limit=15&fields=title,authors,year,abstract,citationCount,venue,externalIds,publicationDate,tldr
```

### 3. Search arXiv

```
https://export.arxiv.org/api/query?search_query=all:{keyword1}+AND+all:{keyword2}&start=0&max_results=15&sortBy=relevance&sortOrder=descending
```

### 4. Look for Method-Specific Signals

When evaluating papers, prioritize:
- **Concrete method**: Does it propose a specific algorithm/architecture/technique?
- **Experimental results**: Does it report metrics on standard benchmarks?
- **Open source**: Is code available? (check for "code", "github", "open source")
- **Reproducibility**: Are experimental details sufficient?
- **Direct relevance**: Can the method be directly applied to the user's problem?

### 5. Present Results

```
## 🛠 方法文献 — {problem}

共找到 N 篇相关方法论文:

### 🎯 最直接可参考
1. **{Title}** ({Year})
   作者: {Authors} | 出处: {Venue} | 引用: {citationCount}
   方法: {使用的具体方法/技术}
   实验: {数据集 + 指标 + 主要结果}
   代码: {是否有开源 / GitHub 链接}
   💡 对你问题的价值: {为什么这个方法可以直接参考}

### 📋 其他相关方法
...

### 🔗 方法分类
- {Method Category 1}: {paper titles}
- {Method Category 2}: {paper titles}
```

## Notes

- Methods papers are most useful when they include experimental comparison on standard benchmarks
- Note whether the paper provides open-source code — this is a strong signal for methods papers
- For Chinese topics, use both Chinese and English keywords
- If the user mentioned specific baselines or related work, use those as additional search terms
- If a paper's method seems directly applicable, explain WHY in the recommendation
