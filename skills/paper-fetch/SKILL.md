---
name: paper-fetch
description: Get full-text access guidance for a specific paper. Determines the best way to retrieve the full paper — open access, arXiv, or via campus network (CNKI / ScienceDirect).
argument-hint: <paper-title>
---

# Paper Fetch — 全文获取

Given a paper title, determine the best way to access the full text and provide actionable steps.

## Arguments

$ARGUMENTS contains the paper title or DOI.

## Workflow

### 1. Identify the Paper

Search Semantic Scholar by title:

```
https://api.semanticscholar.org/graph/v1/paper/search?query={title}&limit=3&fields=title,authors,year,externalIds,openAccessPdf,arxivId,doi,venue
```

Check if the paper has an open-access PDF or arXiv ID.

### 2. Determine Access Routes

Classify the paper into one of these categories:

**A. Open Access Available** — Can fetch directly
- arXiv: `https://arxiv.org/pdf/{arxivId}.pdf`
- Semantic Scholar OpenAccessPDF: direct link
- PubMed Central: for biomedical papers

**B. Closed Access — CNKI (Chinese papers)**
- If the paper appears to be from a Chinese journal
- Guide user to: `/cnki-download "{title}"` (requires Chrome DevTools MCP + campus network)
- Check if cnki-skills is available

**C. Closed Access — ScienceDirect / Elsevier**
- If the paper is from an Elsevier journal
- Guide user to: `/sd-download {paper-id}` (requires Chrome DevTools MCP + campus network/institutional login)
- Check if sd-skills is available

**D. Other Publisher**
- Guide user to check their institution's library access
- Suggest searching Google Scholar

### 3. Present Results

```
## 📄 全文获取 — {Title}

作者: {Authors} | 年份: {Year} | 出处: {Venue}

### ✅ 开放获取
{arXiv PDF link / Open Access PDF link / DOI link}

### 🔒 校园网途径
{CNKI / ScienceDirect / Publisher link}
操作: /cnki-download "{title}"  (需知网登录+校园网)

### 📝 引用
{GB/T 7714 or BibTeX citation}
```

## Notes

- Always prefer open-access routes first
- Only recommend CNKI/ScienceDirect routes if those skills are installed
- If the paper cannot be found, suggest Google Scholar search
- For DOIs, the direct link is `https://doi.org/{doi}`
