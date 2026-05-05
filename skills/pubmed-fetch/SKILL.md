---
name: pubmed-fetch
description: Get full text of a PubMed paper. Checks PubMed Central (PMC) for OA PDF, or provides access guidance. Use when the user has a PMID or PMCID and wants the full paper.
argument-hint: "<PMID or PMCID>"
---

# PubMed Fetch — 全文获取

Given a PMID or PMCID, retrieve the full text if available via PubMed Central, or guide the user to alternative access.

## Arguments

$ARGUMENTS contains the PMID (e.g. "42043857") or PMCID (e.g. "PMC1234567").

## Workflow

### 1. Identify the Paper

If given a PMID, fetch metadata to check for PMCID:

```
https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi?db=pubmed&id={pmid}&retmode=xml
```

Parse for:
- `<ArticleId IdType="pmc">PMCXXXXXX</ArticleId>` — PMCID (if exists)
- `<ArticleTitle>` — title
- `<ELocationID EIdType="doi">` — DOI

### 2. Open Access (PubMed Central)

If PMCID exists, the paper is available OA via PMC:

**HTML full text:**
```
https://www.ncbi.nlm.nih.gov/pmc/articles/{PMCID}/
```

**PDF download:**
```
https://www.ncbi.nlm.nih.gov/pmc/articles/{PMCID}/pdf/
```

**Direct PDF (if available):**
```
https://www.ncbi.nlm.nih.gov/pmc/articles/{PMCID}/pdf/{PMCID}.pdf
```

Use the Chrome DevTools MCP `navigate` tool to open the PDF URL. Then check `document.contentType` — if `application/pdf`, trigger download with:

```javascript
(() => {
  const a = document.createElement('a');
  a.href = window.location.href;
  a.download = '{PMCID}.pdf';
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  return 'downloaded';
})()
```

### 3. No OA Available

If no PMCID exists, the paper is behind a paywall:

- Check if the journal is on ScienceDirect → guide to `/sd-download`
- Check if it's a Chinese journal → guide to `/cnki-download`
- Otherwise → provide the DOI link and suggest checking institutional/library access

### 4. Present Results

```
## PubMed 全文获取 — {Title}

PMID: {pmid} | DOI: {doi}

### ✅ 开放获取 (PubMed Central)
{PMC PDF link}

### 🔒 非开放获取
{DOI link}
需要校园网或机构订阅访问。
```
