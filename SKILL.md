---
name: amazon-ip-risk-screener
description: Screen one Amazon direct competitor for possible third-party intellectual-property signals from a user-provided H10 natural-keyword spreadsheet and exact Amazon product URL, then use bounded source research and product-image comparison when a clue is found. Use for focused Amazon IP-risk screening, not comprehensive legal clearance or general hot-selling analysis.
---

# Amazon IP Risk Screener

Screen one direct competitor at a time. Treat the result as an evidence-based risk screen, not a legal infringement opinion.

## Hard Input Gate

Require exactly these two mandatory inputs before starting:

1. A readable H10 natural-keyword export for the direct competitor in `.xlsx`, `.xlsm`, `.csv`, or `.tsv` format. The table must contain:

- the complete keyword phrase;
- the target ASIN's natural/organic rank.

2. The exact Amazon product-detail URL for the same direct competitor. The URL must identify the intended Amazon marketplace and actual product/variation to inspect.

An ASIN recovered from a filename, a bare ASIN, a search-results URL, or an approximate product link does not replace the exact Amazon product URL. Other rights-holder pages, registry results, reference images, and authorization sources are research evidence gathered by the skill when needed; they are not user-required inputs.

Do not replace the required table with a screenshot, a newly collected live H10 result, advertising keywords, or another ASIN's export. If either mandatory input is missing, the file is unreadable, either required column is absent, or the table and product URL cannot be tied to the same direct competitor, stop and state exactly what is needed.

## Deterministic Filter

Run `scripts/filter_h10_top10.py` on the supplied table. It validates the input and returns every distinct keyword whose natural rank is from 1 through 10 inclusive. It does not take the first 10 rows and does not depend on the table's current sort order.

Use the current Python environment. If `openpyxl` is unavailable for an Excel input, load the bundled workspace dependencies and rerun with their Python executable. Do not manually replace a successful script result.

Example:

```powershell
python scripts/filter_h10_top10.py "C:\path\US_AMAZON_cerebro_B0XXXXXXXX_2026-09-08.xlsx"
```

Record the source file, worksheet, detected columns, total data rows, valid natural-rank rows, and number of distinct rank-1-to-10 keywords. An empty but successfully parsed rank-1-to-10 result is valid evidence.

## Semantic Screening

Review all filtered keyword phrases. Extract only plausible third-party clues, including:

- character, franchise, book, film, game, song, artwork, team, artist, studio, publisher, or brand names;
- protected names or recognizable slogans;
- fixed phrases whose meaning or ownership cannot be explained confidently from ordinary language.

Normalize spelling variants of the same clue into one record. Preserve every containing keyword phrase, its natural rank, the clue's best rank, and its occurrence count within the filtered set.

Do not treat a keyword as protected merely because it is capitalized or specific. Common animals, plants, occupations, mythological figures, historical figures, ordinary decorative styles, and generic product descriptions are not automatically third-party IP. A potentially public-domain or generic named subject may still proceed to research when its status is unclear.

If no plausible clue remains, output `本轮未发现明显IP线索` and stop. This conclusion is limited to the supplied H10 natural keywords ranked 1-10.

## Bounded Research And Image Comparison

For each plausible clue, perform no more than three focused web queries. Prefer sources from the rights holder, brand, team, artist, studio, publisher, official registry, museum, or an identifiable authorized licensee. Reposts, marketplace listings, and unattributed image collections are weak supporting sources and cannot establish ownership by themselves.

When more than five distinct clues survive semantic screening, research the five with the best natural ranks first and mark the remainder `未核验`, rather than silently treating them as safe.

For each researched clue:

1. Determine what the term refers to and whether it identifies a third-party IP, a generic subject, or a public-domain subject.
2. Preserve the source-page URL and, when available, an official or authorized reference-image URL.
3. Open the target Amazon product page and preserve the target main-image source.
4. Compare the reference image with the target main image. Record concrete matched and different features, such as names, logos, facial design, silhouette, costume, pose, props, composition, or other distinctive expression.

Do not use a percentage-similarity threshold. Do not infer infringement from a keyword alone, a broad theme, or an unspecified visual resemblance. If the product page or necessary image evidence is unavailable after a clue is found, preserve the clue and output `信息不足，无法判断` for that clue.

## Conclusions

Assign one result to every reviewed clue:

- `明确第三方IP`: a reliable source confirms a specific third-party IP and the target visibly uses its identifiable name, logo, character, image, or distinctive core expression. This does not establish missing authorization or legal infringement.
- `疑似IP，人工复核`: a third-party clue exists, but ownership, visual correspondence, protected expression, or authorization remains uncertain.
- `通用题材，具体造型需独立设计`: the subject is generic or public domain, but a particular modern depiction, sculpture, graphic, or product design may still be protected.
- `本轮未发现明显IP线索`: no plausible clue appears in the supplied rank-1-to-10 natural keywords.
- `信息不足，无法判断`: required H10 fields, ASIN identity, source evidence, Amazon main image, or comparison evidence is unavailable or inconclusive.

When several clues have different outcomes, show every clue and use the highest-risk supported result as the overall result. Never relabel `明确第三方IP` as `已确认侵权` without checking authorization and the applicable legal rights.

## Output

Return a concise result in the conversation unless the user requests a saved file. Include:

1. overall result;
2. source file, target ASIN, inspected rank scope, and inspected keyword count;
3. an evidence table with `疑似词`, `最佳自然排名`, `包含它的关键词`, `出现次数`, `来源`, `视觉相同点`, `视觉不同点`, and `单项结论`;
4. the status of trademark, copyright, patent, design, and authorization-database checks;
5. a limitation statement that the screen covers only the supplied H10 natural keywords ranked 1-10 and is not comprehensive legal clearance.

Do not invent a database check, source, image comparison, authorization status, or negative finding. Use `未核验` or `信息不足，无法判断` when applicable.
