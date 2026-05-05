---
name: paper-search
description: Multi-source literature search. Searches across PubMed, Semantic Scholar, arXiv, CNKI, and ScienceDirect based on the user's language and domain. Evaluates abstracts and presents ranked results.
argument-hint: <topic>
---

# Paper Search — 多源文献检索

Search across multiple academic databases based on the user's language and domain needs. This is the **retrieval phase** entry point — it finds papers, does NOT download them.

## Arguments

$ARGUMENTS contains the research topic, question, or keywords.

## Workflow

### 1. Analyze the Request

Determine from the query:
- **Language**: Chinese → prioritize CNKI; English → prioritize PubMed/Semantic Scholar/arXiv
- **Domain**: Biomedical → prioritize PubMed; CS/Engineering → Semantic Scholar + arXiv; Chemistry → SD; Unknown → all
- **Paper type**: Survey/frontier/methods (from keywords in query)
- **Specific sources mentioned**: e.g. "pubmed上查" → that source first

### 2. Generate Keywords

- Extract core technical terms from the query
- If Chinese input, generate English translation
- Identify MeSH terms if biomedical domain

### 3. Select Sources (优先级排序)

**通用优先级：** Semantic Scholar → ScienceDirect → IEEE → arXiv → PubMed → CNKI
**工业工程/自动化：** IEEE → Semantic Scholar → arXiv → ScienceDirect → CNKI

| 场景 | 数据源（按优先级） |
|------|-------------------|
| 中文文献（中文期刊/硕博） | CNKI |
| 工业工程 / 自动化 / 制造 | IEEE Xplore → Semantic Scholar → arXiv → SD → CNKI |
| 生物医学 / 生命科学 | PubMed → Semantic Scholar |
| CS / 人工智能 / 软件 | Semantic Scholar → arXiv → IEEE |
| 化学 / 材料 | Semantic Scholar → ScienceDirect |
| 综合 / 不确定 | Semantic Scholar → SD → arXiv → PubMed → CNKI |
| 用户指定来源 | 指定的优先 |

### 4. Execute Searches

For each selected source, call the corresponding API or skill:

**PubMed:**
```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term={keywords}&retmax=8&retmode=json
+ efetch for details
```

**Semantic Scholar:**
```
https://api.semanticscholar.org/graph/v1/paper/search?query={keywords}&limit=8&fields=title,authors,year,abstract,citationCount,venue,externalIds
```

**arXiv:**
```
https://export.arxiv.org/api/query?search_query=all:{keywords}&start=0&max_results=8&sortBy=relevance&sortOrder=descending
```

**CNKI:** `/cnki-search {chinese_keywords}` (if skill available)
**ScienceDirect:** `/sd-search {keywords}` (if skill available)

### 5. Merge and Deduplicate

- Combine results from all sources
- Deduplicate by title similarity
- Keep source metadata for each paper

### 6. Evaluate

For each unique paper, read the abstract and assess:
- **Relevance**: High / Medium / Low
- **Type match**: Survey / Frontier / Methods / General
- **Quality**: Citation count, venue reputation, recency

### 7. Present

```
## 📖 文献检索结果 — {topic}

共找到 N 篇相关论文（来自 {sources}）

### ⭐ 高相关度
1. **{Title}** ({Year})
   作者: {Authors} | {Venue} | 引用: {citationCount}
   {Abstract TLDR / first 2 sentences}
   来源: {PubMed / Semantic Scholar / arXiv / CNKI / SD}
   🔓 OA / 🔒 校园网

### 📋 中等相关度
...
```

## Notes

- This skill is for **search only** — use `/paper-fetch` to get full text
- If rate-limited (Semantic Scholar), retry or skip that source
- For arXiv, use `https://export.arxiv.org` (not http)
- If a source is unavailable, skip gracefully and note it
