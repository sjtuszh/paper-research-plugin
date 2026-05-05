---
name: paper-fetch
description: Get full text access for a specific paper. Entry point for the retrieval phase — given a paper title/DOI/PMID, finds the best access route (OA first, then campus network).
argument-hint: "<paper-title or DOI or PMID>"
---

# Paper Fetch — 全文获取

Given a paper identifier, find and retrieve the full text. This is the **retrieval phase** — only called when the user wants a specific paper's full content.

## Arguments

$ARGUMENTS contains the paper title, DOI, PMID, or URL.

## Workflow

### 1. Identify the Paper

Determine what identifier format the user provided:

**DOI** (starts with `10.`):
```
https://api.semanticscholar.org/graph/v1/paper/search?query={doi}&limit=1&fields=title,authors,year,externalIds,openAccessPdf,arxivId,doi,venue
```

**PMID** (numeric, from PubMed):
- Fetch via E-utilities to get PMCID, DOI
```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id={pmid}&retmode=xml
```

**Title** (free text):
```
https://api.semanticscholar.org/graph/v1/paper/search?query={title}&limit=3&fields=title,authors,year,externalIds,openAccessPdf,arxivId,doi,venue
```

### 2. Determine Access Route

Check in order:

**Priority A — Open Access:**
- **arXiv**: `https://arxiv.org/pdf/{arxivId}.pdf`
- **PubMed Central**: `https://www.ncbi.nlm.nih.gov/pmc/articles/{PMCID}/pdf/`
- **Semantic Scholar**: `openAccessPdf` URL

**Priority B — Campus Network:**
- **ScienceDirect (Elsevier)**: Guide to `/sd-download {PII}` (needs campus + MCP)
- **CNKI (Chinese)**: Guide to `/cnki-download "{title}"` (needs campus + MCP)

**Priority C — DOI Link:**
- Provide `https://doi.org/{doi}` for manual access

### 3. Download (if OA)

If open access URL is available, use Chrome DevTools MCP to:
1. Navigate to the PDF URL
2. Check `document.contentType === 'application/pdf'`
3. Trigger download via JS `a.click()`

### 4. Present

```
## 📄 全文获取 — {Title}

作者: {Authors} | 年份: {Year} | 出处: {Venue}

### ✅ 开放获取
{PDF link}

### 🔒 校园网途径
{SD/CNKI command or DOI link}
```

## Notes

- Always prefer open access first
- For PMCID → PMC PDF, the direct URL pattern is `https://www.ncbi.nlm.nih.gov/pmc/articles/{PMCID}/pdf/`
- If paper is from an Elsevier journal, use `/sd-download` with the PII
- If paper is a Chinese journal article, use `/cnki-download`
- If MCP is not running, just show the link and tell the user to open it manually
