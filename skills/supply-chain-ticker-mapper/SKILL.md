---
name: supply-chain-ticker-mapper
description: Use whenever the user shares an industry analysis, supply-chain breakdown, technology trend article, or bottleneck/component report (e.g. NVIDIA AI servers, semiconductors, EVs, batteries) and wants to know WHICH COMPANIES are relevant and WHAT THEIR STOCK TICKERS are. Trigger on phrases like "find relevant companies and tickers", "which stocks benefit from this", "map this to tickers", "supply chain stocks", "誰是相關供應商", "股票代號", or any request to turn technologies/components/bottlenecks into an investable company list. For each category in the source material, researches and lists up to the top ~5 public companies per country (Taiwan TWSE/TPEx, US NASDAQ/NYSE, Japan TSE, Korea KRX first, then others), producing a table with company name, ticker, exchange, and rationale, grouped by category then market. Does NOT fetch live prices; identification/mapping only (use stock-analysis or yfinance for valuation).
---

# Supply Chain → Company → Ticker Mapper

Turns a technology/industry/bottleneck writeup into a structured, sourced table of relevant publicly-traded companies and their stock tickers, organized by category.

## When to use this

The user wants to identify **which public companies play in a given technology/industry/supply chain**, with correct ticker + exchange. This works in two modes:

- **Document mode**: the user has pasted or referenced content describing a technology stack, supply chain, or industry bottleneck (components, materials, process steps, sub-systems). Categories come from that source material.
- **Topic mode**: the user just names a theme directly with no source document — e.g. "find companies and tickers for LLM AI", "humanoid robotics supply chain stocks", "EV battery stocks by country". No document is required to trigger this skill; if the user names a sector, theme, or technology and asks for relevant companies/tickers, proceed directly into research — do NOT ask them to paste an article first. Treat the absence of a document as a signal to build the category breakdown yourself (see Step 1), not as a blocker.

Common triggers:
- "help me find relevant companies and their stock tickers" (with or without a pasted report)
- "who supplies X" / "誰是這個供應鏈的廠商"
- "turn this into a stock watchlist"
- "[topic] stocks by country" / "[topic] 概念股"
- Any of the example documents this skill was built around: NVIDIA AI server bottlenecks, passive components (MLCC/inductors/capacitors), or AI server hardware sub-systems (cooling, power, optics, PCB)

This skill explicitly does **not** fetch live prices, valuations, or run technical/fundamental analysis. It only identifies and maps. If the user also wants quotes or analysis, hand off to the `stock-analysis` skill or `yfinance` tools after the mapping table is produced — mention this as a next step rather than doing it automatically.

## Workflow

### Step 1: Establish the categories

**If the user provided source material** (pasted, uploaded, or referenced): read it and break it into the **distinct technical categories or bottleneck areas** it describes. Use the source's own structure where possible (its headers/sections) rather than inventing a new taxonomy.

**If the user only named a topic with no source document** (e.g. "LLM AI", "humanoid robots", "EV batteries"): build the category breakdown yourself before researching companies. Use `web_search` to ground this in the topic's actual value chain rather than guessing from general knowledge alone, especially for fast-moving sectors. A reasonable default approach:
1. Search for an overview of the topic's value chain / supply chain (e.g. `"LLM AI" supply chain layers companies`, `LLM 產業鏈`)
2. Break it into distinct layers/categories the way an analyst would
3. Confirm the breakdown makes sense for the specific topic rather than reusing a previous topic's categories verbatim — "LLM AI" and "EV batteries" should NOT share a category list

**Don't stop at the software/systems-level abstraction — push down to the physical/electrical layer too.** A topic like "LLM AI" tends to get decomposed at the level of model → chip → server → cloud, which silently omits the physical hardware that makes those servers run (this is a known failure mode: it's easy to list "power & datacenter infrastructure" as one vague catch-all and skip the actual component tier inside it). Whenever the topic ultimately runs on physical servers/racks (true for any AI/compute topic, and for EVs, robotics, industrial hardware, etc.), explicitly include these as their own layers, not folded into a vaguer parent category:
- HBM / memory supply
- Liquid cooling (cold plates, CDU, quick disconnects, manifolds)
- Networking / optical transceivers / CPO
- Power delivery (PSU, busbar, VRM, TLVR inductors)
- **Passive components (MLCC, inductors, resistors, capacitors)** — easy to drop entirely since it has no single famous "brand," but every powered electronic system depends on it
- PCB / substrate / copper cabling
- Advanced packaging (CoWoS or equivalent)
- Peripheral ICs (PCIe switch/retimer, BMC, PMIC)

Plus whatever higher-level/software layers are specific to the topic (e.g. for "LLM AI": foundation model developers, cloud/compute infrastructure, inference serving & API platforms; for "EV batteries": cell chemistry, cell manufacturing, BMS, charging infrastructure).

If the topic/source material covers a different domain entirely (e.g. pure software, biotech, finance) where the physical-layer checklist above doesn't apply, derive the equivalent category breakdown from that domain instead — don't force-fit a hardware taxonomy onto unrelated content. But default to assuming the physical layer is relevant rather than assuming it isn't; it's the layer most likely to be silently skipped.

**Before moving to Step 2, re-read the category list and ask: "if this whole list were a parts list for one physical server, what's missing?"** This catches categories like passive components that don't have an obvious single representative company and so are easy to skip when brainstorming layers from memory.

### Step 2: For each category, identify candidate companies — top players per priority market

For each category, don't stop at the first few names that come to mind. Run a structured pass through the priority market order (default **Taiwan → US → Japan → Korea → other**), aiming for up to ~5 real candidates per market where that many genuinely exist (some categories will only have 1-2 legitimate players in a given market — don't pad with weak fits just to hit 5).

For categories with deep, fragmented supply chains (passive components, PCB/substrate, cooling, power), use `web_search` per market to verify the current top players rather than relying on recall — these rankings shift with M&A, capacity expansion, and earnings cycles. Useful query patterns:
- `"<category>" 排名 廠商 2026` (Taiwan-language industry roundups reliably name the top 4-6 listed players together)
- `"<category>" companies AI server market share` (for US/global)
- Site-specific checks for Japan (TSE) and Korea (KRX) leaders if the category likely has them (passives, memory, optics)

Watch for two ownership patterns that change how a "top player" should be listed:
- **Wholly-owned subsidiaries not separately traded** (e.g. Cyntec under Delta, AVX under Kyocera) — map to the parent ticker, noted in rationale.
- **Cross-border acquisitions that moved a brand to a different market's ticker** (e.g. KEMET, once US-listed, is now owned by Yageo/Taiwan) — list under the current parent's market and ticker, not the brand's historical home market.

If you're not confident about a specific company-to-category mapping (especially niche component suppliers), use `web_search` to verify rather than guessing. This matters most for:
- Newer/smaller suppliers not in your training data
- Recent supply agreements or design wins (these change fast and are exactly the kind of claim that needs verification)
- Any company name that came from the user's own document text — verify it's still accurate/current, don't just relay it uncritically

Do NOT search for basic, stable facts (e.g. that TSMC does advanced packaging, that SK Hynix/Samsung/Micron make HBM) — answer those from knowledge.

### Step 3: Resolve each company to its primary ticker(s)

For each company, find:
- **Primary ticker** on its **home exchange** (TWSE 4-digit code, TSE 4-digit code, KRX 6-digit code, or US ticker)
- If it's also commonly traded via ADR or a secondary US listing that the user is more likely to access, note that as a secondary ticker
- **Exchange** explicitly (e.g. "TWSE: 2330", "NASDAQ: NVDA", "TSE: 6920", "KRX: 000660")

If uncertain about an exact ticker (tickers are exact-match facts that are easy to get subtly wrong — wrong suffix, wrong exchange, delisted/renamed company), verify with `web_search` rather than stating it with false confidence. Getting a ticker wrong is worse than omitting it — if you can't verify, say so in the rationale column rather than guessing.

**Wholly-owned subsidiaries that aren't separately listed:** Many real suppliers (e.g. Cyntec/乾坤, which makes TLVR inductors, is a wholly-owned subsidiary of Delta Electronics) are not independently traded. In this case, map the row to the **parent company's ticker** and note the subsidiary relationship in the rationale (e.g. "TLVR inductors, via subsidiary Cyntec — Delta owns 100%"). Don't invent a ticker for the subsidiary, and don't silently drop it either — the parent ticker is still the correct investable proxy.

### Step 4: Build the output table

Produce one table per category (or one combined table with a Category column for shorter requests), with these columns:

| Company | Ticker | Exchange | Rationale |
|---|---|---|---|

- **Company**: Company name (include Chinese/Japanese/Korean name alongside English if the source material used it)
- **Ticker**: Exact ticker/code
- **Exchange**: TWSE / TPEx / NASDAQ / NYSE / TSE / KRX / other
- **Rationale**: One short clause — what specifically they supply or why they're relevant (not a generic company description)

Within each category, group rows by market in priority order (Taiwan → US → Japan → Korea → other), with up to ~5 companies per market where that many genuine candidates exist. Use a sub-heading or blank-row separator per market if the category has candidates from 3+ markets, so the user can see "Taiwan: X, Y, Z / Japan: A, B" at a glance rather than a flat undifferentiated list.

**Before finalizing, run two sanity checks:**
1. **Missing rows within a category**: for every category, ask "who is the market leader in the #1 priority market (Taiwan, per default) for this specific niche?" and confirm that company is in the table — not just whichever companies were easiest to recall. A category populated only with Japan/US names while its Taiwan leader is missing is a sign the search wasn't run separately per market.
2. **Missing categories entirely**: re-scan the full category list against the physical/electrical checklist from Step 1 (HBM, cooling, power delivery, **passive components**, PCB/substrate, advanced packaging, peripheral ICs). If the topic involves physical servers/hardware and any of these layers isn't represented as its own category, add it — don't let it stay folded silently into a broader category like "power & datacenter infrastructure" where it has no actual row.

### Step 5: Present and offer next steps

After the table, briefly note:
- Any categories where you couldn't confidently identify a specific public company (e.g. the supplier is private, or it's a sub-component made in-house by a larger company)
- That live quotes/valuation aren't included — offer to pull them via `yfinance` tools or hand off to the `stock-analysis` skill if the user wants that next

## Output format notes

- Use a markdown table directly in the conversation — this is reference content the user will scan, not a long-form document, so it does NOT need to go into a file/artifact unless the user asks to save it or the table is very large (15+ rows spanning many categories), in which case offer to export to .xlsx via the `xlsx` skill.
- Keep rationale clauses tight (under ~12 words) — this is a scannable reference table, not prose.
- Don't editorialize about which stocks are "good buys" — this skill identifies relevant companies, it does not give investment recommendations. If the user asks for that, note that Claude isn't a financial advisor and can present factual information (e.g. via stock-analysis) rather than a recommendation.
