---
name: pubmed-search
description: Search PubMed (NCBI) for biomedical literature. Use when the user wants to find medical, biological, or life science papers. Uses NCBI E-utilities API (free, no key needed).
argument-hint: <search query>
---

# PubMed Search — 生物医学文献检索

Search PubMed using NCBI E-utilities API. Returns PMID, title, authors, journal, year, DOI, and abstract.

## Arguments

$ARGUMENTS contains the search query. Supports standard PubMed syntax (AND, OR, MeSH terms, etc.).

## Workflow

### 1. Search (E-utilities esearch)

Call the NCBI esearch API to find PMIDs:

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term={query}&retmax=10&retmode=json
```

Optional parameters:
- `&mindate=2024&maxdate=2026&datetype=pdat` — filter by publication date
- `&reldate=365` — last 365 days
- `&sort=relevance` — sort by relevance (default) or `&sort=pub_date` for newest

### 2. Fetch details (E-utilities efetch)

Take the PMID list from step 1 and fetch full metadata:

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id={comma_separated_pmids}&retmode=xml
```

Parse the XML response to extract for each paper:
- **PMID** — unique identifier
- **Title** — article title
- **Authors** — author list
- **Journal** — journal name
- **Year** — publication year
- **DOI** — DOI (from `ELocationID` with `EIdType="doi"`)
- **Abstract** — abstract text
- **PMCID** — PubMed Central ID (if available, means OA full text)

### 3. Present Results

Format as a structured list:

```
## PubMed 检索结果 — {query}

共找到 {total} 篇，显示前 {n} 篇

### {Paper n}
**{Title}** ({Year})
作者: {Authors}
期刊: {Journal} | PMID: {pmid}
摘要: {Abstract first 200 chars}
DOI: {doi}
📖 开放获取: {Yes if PMCID exists / No}
```

## Notes

- PubMed E-utilities are **free**, no API key required
- Without API key: 3 requests per second limit
- To check OA status: if the paper has a PMCID, it's in PubMed Central (free full text)
- The `&retmode=json` for esearch makes parsing easy; efetch only supports XML
- For complex queries, use PubMed's native syntax: `(term1[MeSH]) AND (term2[Title/Abstract])`
- If the result has too many papers, suggest refining the query
