---
name: paper-survey
description: Search for survey and review papers on a given research topic. Use when the user wants to find comprehensive review/survey literature, understand a field overview, or get started in a new research direction.
argument-hint: <research-topic>
---

# Paper Survey — 综述文献检索

Search for survey/review papers on a given research topic. Returns titles, authors, abstracts, citation counts, and relevance assessment.

## Arguments

$ARGUMENTS contains the research topic (Chinese or English).

## Workflow

### 1. Search Semantic Scholar for surveys

Call the Semantic Scholar API to find survey/review papers:

```
https://api.semanticscholar.org/graph/v1/paper/search?query=survey {topic}&limit=10&fields=title,authors,year,abstract,citationCount,venue,externalIds,publicationDate,tldr
```

Also search with "review" as a complement:

```
https://api.semanticscholar.org/graph/v1/paper/search?query=review {topic}&limit=10&fields=title,authors,year,abstract,citationCount,venue,externalIds,publicationDate,tldr
```

### 2. Search arXiv for surveys

```
https://export.arxiv.org/api/query?search_query=all:{topic}+AND+all:survey&start=0&max_results=10&sortBy=relevance&sortOrder=descending
```

### 3. If user's topic is in Chinese, also try English translation

Generate English keywords from the Chinese topic and repeat steps 1-2.

### 4. Evaluate & Filter

For each paper returned, evaluate:
- **Is it a survey/review?** Check title for "survey", "review", "overview", "tutorial", "comprehensive", "systematic", "literature review", "研究综述", "展望"
- **Relevance**: Does it substantively cover the user's topic?
- **Quality signals**: Citation count, venue reputation, recency
- **Coverage breadth**: Does it comprehensively cover the field or just a narrow aspect?

### 5. Present Results

Format results as:

```
## 📚 综述文献 — {topic}

共找到 N 篇相关综述（Semantic Scholar: X, arXiv: Y）:

### 高相关度
1. **{Title}** ({Year})
   作者: {Authors} | 出处: {Venue} | 引用: {citationCount}
   摘要: {Abstract TLDR or first 2 sentences}
   推荐理由: {为什么这是篇好综述，覆盖了哪些方面}
   获取: {Open Access / arXiv / 需校园网}

### 中等相关度
...

### 补充
- 搜索关键词: survey {topic}, review {topic}
- 建议使用 /paper-fetch 获取全文
```

## Notes

- Semantic Scholar API is free and requires no API key for basic search
- arXiv API returns XML — parse the `<entry>` elements for title, authors, abstract, published date
- If Semantic Scholar rate-limits, fall back to arXiv-only results
- For Chinese topics, try both Chinese keywords and English translations
