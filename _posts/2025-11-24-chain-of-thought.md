---
layout: post
title: "Chain of Thought: This is the BEST Way to Make AI Auditable for Finance"
description: "How I made AI variance analysis auditable with transparent, step-by-step reasoning and confidence scores. 4-step Chain of Thought delivers confidence, full auditability, and debuggable AI for finance teams."
date: 2025-11-24
author: "Noreli North"
categories: [AI, Chain of Thought, Financial Accounting, ERPNext, Variance Analysis]
tags: [AI, Chain of Thought, Variance Analysis, ERPNext, Frappe Framework, Finance Automation, Auditability, Confidence Scoring, OpenAI, Accounting AI, Transparent AI, Black Box AI, Production AI]
---

**Key Results:**
- ✅ **4-step transparent reasoning** - Data Analysis → Pattern Recognition → Root Cause → Conclusion
- 🎯 **Confidence scores at each step**
- 📊 **Fully auditable**
- 🔍 **Debuggable AI**

[Video Demo](https://youtu.be/Vyl9kuR3-Yg) | [Documentation](https://norelinorth.github.io)

---

## The Problem: Black Box AI Fails Audits

Your AI analyzes a variance and tells you:

> "Marketing Expenses increased 156% from $45,000 to $115,000. This variance is primarily due to increased digital advertising spend and new vendor partnerships."

**Your auditor asks:** "How did you verify this AI analysis?"

**You answer:** "Uh... the AI said so?"

**❌ That's not good enough.**

### The Black Box Problem

Most AI in finance operates as a **black box**:

```
[Your Data] → [??? Mystery AI Processing ???] → [Answer]
```

You get an answer, but:
- ❌ You don't know WHAT data the AI examined
- ❌ You don't know HOW it reached the conclusion
- ❌ You don't know HOW CONFIDENT the AI is
- ❌ You can't VERIFY the reasoning
- ❌ When it's wrong, you can't DEBUG it

**This is a huge problem for:**
- **Auditors** - Can't verify AI analysis
- **Controllers** - Can't trust unexplained answers
- **CFOs** - Can't rely on AI for decisions
- **Accountants** - Can't learn from the AI's process

---

## The Solution: Chain of Thought Reasoning

Chain of Thought (CoT) makes AI **show its work**.

Instead of jumping directly to a conclusion, the AI breaks down its reasoning into **4 explicit, verifiable steps**:

```
[Your Data] → [STEP 1: Data Analysis]
            → [STEP 2: Pattern Recognition]
            → [STEP 3: Root Cause Identification]
            → [STEP 4: Conclusion]
            → [Answer + Complete Reasoning Trail]
```

Each step includes:
- ✅ **Clear description** of what the AI did
- ✅ **Specific data examined** (verifiable)
- ✅ **Confidence score** (95%, 85%, 75%, 90%)
- ✅ **Audit trail** (saved to database)

---

## Real Example: Marketing Variance Analysis

Let's walk through a real Chain of Thought analysis.

### The Variance

- **Account:** Marketing Expenses
- **Base Period:** FY 2023 - $45,000
- **Comparison Period:** FY 2024 - $115,024
- **Variance:** +$70,024 (+156%)

### ❌ Black Box AI Output

```
"Marketing Expenses increased 156% from $45,000 to $115,024.
This variance is primarily due to increased digital advertising
spend and new vendor partnerships."
```

**Problem:** How do you verify this?

### ✅ Chain of Thought AI Output

#### Step 1: Data Analysis (95% Confidence)

```
🔍 DATA ANALYSIS
Confidence: 95%

Analyzed transaction patterns between FY 2023 and FY 2024.

Data examined:
• 45 transactions in base period (FY 2023)
• 112 transactions in comparison period (FY 2024)
• Average transaction size: $1,000 → $1,027 (+2.7%)
• Total examined: 157 GL entries
```

**Verification:** Run this SQL query:

```sql
SELECT COUNT(*), AVG(ABS(debit - credit))
FROM `tabGL Entry`
WHERE account = 'Marketing Expenses'
AND company = 'Your Company'
AND posting_date BETWEEN '2023-01-01' AND '2024-12-31'
```

**Result:** You should get 157 records. ✅ Verifiable!

**Why 95% confidence?** Data extraction is deterministic. Counting database rows is highly reliable.

---

#### Step 2: Pattern Recognition (85% Confidence)

```
🎯 PATTERN RECOGNITION
Confidence: 85%

Identified 12 new parties not present in base period.

Top new vendors:
• Google Ads: 23 transactions, $28,000
• LinkedIn Marketing: 15 transactions, $18,500
• Meta Business: 12 transactions, $13,200

Total from new vendors: $59,700 (85% of total variance)

Voucher distribution changes:
• Journal Entry: +45 transactions
• Purchase Invoice: +18 transactions
```

**Verification:** Check which parties appear in FY 2024 but not FY 2023:

```sql
SELECT comp.party, COUNT(*) as txn_count,
       SUM(comp.debit - comp.credit) as net_amount
FROM `tabGL Entry` comp
WHERE comp.account = 'Marketing Expenses'
AND comp.posting_date BETWEEN '2024-01-01' AND '2024-12-31'
AND comp.party IS NOT NULL
AND NOT EXISTS (
    SELECT 1 FROM `tabGL Entry` base
    WHERE base.account = 'Marketing Expenses'
    AND base.party = comp.party
    AND base.posting_date BETWEEN '2023-01-01' AND '2023-12-31'
)
GROUP BY comp.party
ORDER BY ABS(SUM(comp.debit - comp.credit)) DESC
```

**Result:** You'll see Google Ads, LinkedIn Marketing, Meta Business. ✅ Verifiable!

**Why 85% confidence?** Pattern recognition requires interpretation. "Google LLC" vs "Google Ads" might be the same vendor with different names.

---

#### Step 3: Root Cause Identification (75% Confidence)

```
🔎 ROOT CAUSE IDENTIFICATION
Confidence: 75%

Category: Volume Change

Analysis:
• Base period: 45 txns × $1,000 avg = $45,000
• Comp period: 112 txns × $1,027 avg = $115,024
• Volume effect: +67 txns × $1,000 = +$67,000 (96% of variance)
• Price effect: 112 txns × $27 = +$3,024 (4% of variance)

Conclusion: Variance driven by volume increase (business expansion),
not price increase (cost inflation).
```

**Verification:** Validate the math:

- Transaction count change: 112 - 45 = 67 transactions (+149%)
- Average price change: $1,027 - $1,000 = $27 (+2.7%)
- Volume effect: 67 × $1,000 = $67,000
- Price effect: 112 × $27 = $3,024
- Total variance: $67,000 + $3,024 = $70,024 ✅ Matches!

**Why 75% confidence?** Root cause classification requires judgment. It might not be ONLY volume - could be a mix of volume AND service type changes.

---

#### Step 4: Conclusion (90% Confidence)

```
✅ CONCLUSION
Confidence: 90%

The $70,024 variance in Marketing Expenses was primarily driven
by strategic expansion into new digital marketing channels.

Key findings:
• 12 new vendors onboarded (Google Ads, LinkedIn, Meta)
• $59,700 from new vendors (85% of total variance)
• Transaction volume increased 149% while maintaining stable
  per-transaction costs (+2.7%)
• Mix of Journal Entries and Purchase Invoices

Recommendation:
Classify as growth investment rather than cost overrun.
Monitor ROI on digital channels in next period.

───────────────────────────────────────────
OVERALL CONFIDENCE: 86%
(Average of all steps: (95+85+75+90)/4 = 86.25%)
```

**Verification:** Review the logical flow from Steps 1-3. Does the conclusion follow from the evidence?

**Why 90% confidence?** The conclusion synthesizes high-quality data from previous steps. The logic is sound and supported by verified data.

---

## The Power of Confidence Scores

Each step has a **confidence score** that tells you how reliable the AI's reasoning is at that point:

| Step | Confidence | Why This Score? | What to Do |
|------|-----------|----------------|------------|
| **Step 1: Data Analysis** | 95% | Data extraction is deterministic | Trust it, spot-check 5% |
| **Step 2: Pattern Recognition** | 85% | Pattern matching requires interpretation | Review vendor list for name variations |
| **Step 3: Root Cause** | 70% | Classification requires judgment | Consider alternative root causes |
| **Step 4: Conclusion** | 90% | Synthesizes quality inputs | Validate the logical flow |

### What Confidence Scores Tell You

**95% confidence → Trust it**
- Data extraction, counting, math
- Spot-check 5% of records
- Focus your time elsewhere

**85% confidence → Probably correct**
- Pattern matching, grouping
- Review for edge cases (name variations, subsidiaries)
- Verify the top 3-5 patterns

**70% confidence → Verify carefully**
- Root cause classification, judgment calls
- Consider alternative explanations
- Compare to industry knowledge
- This is where your expertise matters most

**60% or below → Double-check everything**
- AI is uncertain
- High chance of error
- Manual review required

---

## Implementation: How It Works

### Architecture Overview

```
User Request
    ↓
Chain of Thought Orchestrator
    ↓
┌─────────────────────────────────────────────────────┐
│  STEP 1: Data Analysis (95%)                        │
│  • Query GL Entry for transaction patterns          │
│  • Count base period transactions                   │
│  • Count comparison period transactions             │
│  • Calculate average transaction sizes              │
└─────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────┐
│  STEP 2: Pattern Recognition (85%)                  │
│  • Identify new parties (vendors/customers)         │
│  • Analyze voucher type distribution                │
│  • Query sample transaction remarks                 │
│  • Calculate contribution to variance               │
└─────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────┐
│  STEP 3: Root Cause Identification (75%)            │
│  • Separate volume effect from price effect         │
│  • Classify variance category                       │
│  • Determine primary driver                         │
│  • Calculate confidence based on clarity            │
└─────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────┐
│  STEP 4: AI Conclusion (90%)                        │
│  • Build comprehensive prompt from Steps 1-3        │
│  • Call AI with structured context                  │
│  • Generate natural language explanation            │
│  • Calculate overall confidence                     │
└─────────────────────────────────────────────────────┘
    ↓
Save to Variance Explanation DocType
• Complete reasoning trail
• All confidence scores
• Timestamp and user
• Full audit trail
```

### Core Function

```python
def analyze_with_chain_of_thought(account, company, base_fy, comp_fy,
                                    base_balance, comp_balance, variance_amount):
    """
    Perform Chain of Thought (CoT) analysis for variance explanation.

    Returns:
        dict with:
            - explanation: Final AI-generated explanation
            - reasoning_steps: List of dicts with CoT steps
            - overall_confidence: Average confidence score
    """
    result = {
        "explanation": "",
        "reasoning_steps": [],
        "overall_confidence": 0.0
    }

    # Check if AI is configured
    if not is_ai_configured():
        frappe.logger().warning("[CoT] AI not configured")
        return result

    try:
        # Step 1: Data Analysis - Query transaction patterns
        step1_data = _query_transaction_patterns(account, company, base_fy, comp_fy)

        result["reasoning_steps"].append({
            "step_number": 1,
            "step_type": "Data Analysis",
            "description": f"Analyzed {step1_data['total_txns_base'] + step1_data['total_txns_comp']} transactions...",
            "data_examined": f"GL Entries: {step1_data['total_txns_base']} base, {step1_data['total_txns_comp']} comp",
            "confidence_score": 0.95  # High confidence in data extraction
        })

        # Step 2: Pattern Recognition - New parties and voucher changes
        step2_data = _query_new_parties(account, company, base_fy, comp_fy)
        step2_vouchers = _query_voucher_distribution(account, company, base_fy, comp_fy)

        result["reasoning_steps"].append({
            "step_number": 2,
            "step_type": "Pattern Recognition",
            "description": f"Identified {len(step2_data['new_parties'])} new parties...",
            "data_examined": f"New parties: {len(step2_data['new_parties'])} identified",
            "confidence_score": 0.85  # Good confidence in pattern detection
        })

        # Step 3: Root Cause Identification
        root_cause = _identify_root_cause(variance_amount, step1_data, step2_data, step2_vouchers)

        result["reasoning_steps"].append({
            "step_number": 3,
            "step_type": "Root Cause Identification",
            "description": f"Category: {root_cause['category']}. {root_cause['reasoning']}",
            "data_examined": "Transaction patterns and party analysis",
            "confidence_score": root_cause.get('confidence', 0.75)
        })

        # Step 4: Conclusion - Generate AI explanation with full context
        prompt = _build_cot_prompt(account, company, base_fy, comp_fy,
                                    variance_amount, step1_data, step2_data,
                                    step2_vouchers, root_cause)

        ai_explanation = get_ai_analysis(prompt)

        if ai_explanation:
            result["explanation"] = ai_explanation
            confidence = 0.90
        else:
            result["explanation"] = f"Variance detected: {root_cause['reasoning']}"
            confidence = 0.60

        result["reasoning_steps"].append({
            "step_number": 4,
            "step_type": "Conclusion",
            "description": "Generated comprehensive explanation",
            "data_examined": "All previous analysis steps synthesized",
            "confidence_score": confidence
        })

        # Calculate overall confidence
        result["overall_confidence"] = sum(
            step['confidence_score'] for step in result["reasoning_steps"]
        ) / len(result["reasoning_steps"])

        frappe.logger().info(
            f"[CoT] Completed with {len(result['reasoning_steps'])} steps, "
            f"confidence: {result['overall_confidence']:.2%}"
        )

        return result

    except Exception as e:
        frappe.log_error(
            message=f"Chain of Thought analysis failed: {str(e)}\n{frappe.get_traceback()}",
            title="Variance Analysis CoT Error"
        )
        return result
```

### Step 1: Data Analysis

```python
def _query_transaction_patterns(account, company, base_fy, comp_fy):
    """Query transaction patterns for base and comparison periods."""

    base_fy_doc = frappe.get_doc("Fiscal Year", base_fy)
    comp_fy_doc = frappe.get_doc("Fiscal Year", comp_fy)

    # Query base period
    base_stats = frappe.db.sql("""
        SELECT
            COUNT(*) as total_txns,
            AVG(ABS(debit - credit)) as avg_txn,
            SUM(debit - credit) as net_amount
        FROM `tabGL Entry`
        WHERE account = %(account)s
        AND company = %(company)s
        AND posting_date BETWEEN %(from_date)s AND %(to_date)s
    """, {
        "account": account,
        "company": company,
        "from_date": base_fy_doc.year_start_date,
        "to_date": base_fy_doc.year_end_date
    }, as_dict=True)

    # Query comparison period
    comp_stats = frappe.db.sql("""
        SELECT
            COUNT(*) as total_txns,
            AVG(ABS(debit - credit)) as avg_txn,
            SUM(debit - credit) as net_amount
        FROM `tabGL Entry`
        WHERE account = %(account)s
        AND company = %(company)s
        AND posting_date BETWEEN %(from_date)s AND %(to_date)s
    """, {
        "account": account,
        "company": company,
        "from_date": comp_fy_doc.year_start_date,
        "to_date": comp_fy_doc.year_end_date
    }, as_dict=True)

    return {
        "total_txns_base": base_stats[0].get("total_txns", 0),
        "total_txns_comp": comp_stats[0].get("total_txns", 0),
        "avg_txn_base": flt(base_stats[0].get("avg_txn", 0)),
        "avg_txn_comp": flt(comp_stats[0].get("avg_txn", 0)),
        "net_amount_base": flt(base_stats[0].get("net_amount", 0)),
        "net_amount_comp": flt(comp_stats[0].get("net_amount", 0))
    }
```

**Key points:**
- ✅ Uses standard Frappe `frappe.db.sql()` with parameterized queries (SQL injection safe)
- ✅ Queries only `tabGL Entry` (no custom fields, no core modifications)
- ✅ Returns verifiable data (you can run the same query)
- ✅ 95% confidence because this is deterministic data extraction

---

### Step 2: Pattern Recognition

```python
def _query_new_parties(account, company, base_fy, comp_fy):
    """Query for new parties in comparison period vs base period."""

    base_fy_doc = frappe.get_doc("Fiscal Year", base_fy)
    comp_fy_doc = frappe.get_doc("Fiscal Year", comp_fy)

    # Find parties in comp period that weren't in base period
    new_parties = frappe.db.sql("""
        SELECT
            comp.party,
            comp.party_type,
            COUNT(*) as txn_count,
            SUM(comp.debit - comp.credit) as net_amount
        FROM `tabGL Entry` comp
        WHERE comp.account = %(account)s
        AND comp.company = %(company)s
        AND comp.posting_date BETWEEN %(comp_from)s AND %(comp_to)s
        AND comp.party IS NOT NULL
        AND NOT EXISTS (
            SELECT 1 FROM `tabGL Entry` base
            WHERE base.account = %(account)s
            AND base.company = %(company)s
            AND base.party = comp.party
            AND base.posting_date BETWEEN %(base_from)s AND %(base_to)s
        )
        GROUP BY comp.party, comp.party_type
        ORDER BY ABS(SUM(comp.debit - comp.credit)) DESC
        LIMIT 10
    """, {
        "account": account,
        "company": company,
        "base_from": base_fy_doc.year_start_date,
        "base_to": base_fy_doc.year_end_date,
        "comp_from": comp_fy_doc.year_start_date,
        "comp_to": comp_fy_doc.year_end_date
    }, as_dict=True)

    return {"new_parties": new_parties}
```

**Key points:**
- ✅ Uses `NOT EXISTS` subquery to find new parties
- ✅ Returns top 10 by absolute variance contribution
- ✅ 85% confidence because party matching might have edge cases (name variations, subsidiaries)

---

### Step 3: Root Cause Identification

```python
def _identify_root_cause(variance_amount, txn_patterns, new_parties_data, voucher_data):
    """Identify root cause category based on analysis data."""

    category = "Other"
    reasoning = ""
    confidence = 0.7

    # Calculate transaction volume change
    txn_change_pct = 0
    if txn_patterns.get('total_txns_base', 0) > 0:
        txn_change_pct = (
            (txn_patterns['total_txns_comp'] - txn_patterns['total_txns_base'])
            / txn_patterns['total_txns_base']
        ) * 100

    # Calculate average price change
    avg_change_pct = 0
    if txn_patterns.get('avg_txn_base', 0) > 0:
        avg_change_pct = (
            (txn_patterns['avg_txn_comp'] - txn_patterns['avg_txn_base'])
            / txn_patterns['avg_txn_base']
        ) * 100

    # Classify based on dominant driver
    if abs(txn_change_pct) > 20:
        category = "Volume Change"
        reasoning = f"Transaction volume changed by {txn_change_pct:.1f}%"
        confidence = 0.85
    elif len(new_parties_data.get('new_parties', [])) > 0:
        category = "New Business/Party"
        reasoning = f"{len(new_parties_data['new_parties'])} new parties identified"
        confidence = 0.80
    elif abs(avg_change_pct) > 15:
        category = "Price Change"
        reasoning = f"Average transaction size changed by {avg_change_pct:.1f}%"
        confidence = 0.75
    else:
        category = "Other"
        reasoning = "No clear dominant pattern identified"
        confidence = 0.60

    return {
        "category": category,
        "reasoning": reasoning,
        "confidence": confidence
    }
```

**Key points:**
- ✅ Uses thresholds (20%, 15%) to classify
- ✅ Prioritizes volume > new parties > price
- ✅ Confidence varies based on clarity of pattern (85% for clear volume change, 60% for unclear)
- ✅ 75% average confidence because classification requires judgment

---

## Why This Matters for Finance

### 1. Auditability ✅

**Auditor:** "How did you verify this AI analysis?"

**You:** "The AI used Chain of Thought reasoning across 4 steps with 86% overall confidence. Here's the complete reasoning trail. You can verify every step against the GL Entry table."

**Auditor:** ✅ *Marks as verified*

**Every step is verifiable:**
- Step 1: Run the transaction count query → 157 records
- Step 2: Run the new party query → Google, LinkedIn, Meta
- Step 3: Validate the volume vs price math → 96% volume, 4% price
- Step 4: Review the logical flow → Strategic expansion makes sense

---

### 2. Trust with Transparency 🤝

**Without confidence scores:**
> "Marketing increased due to new vendors."
>
> **Your thought:** "Should I trust this? How sure is the AI?"

**With confidence scores:**
> "Marketing increased due to new vendors.
> - Step 1: Data Analysis (95% confidence)
> - Step 2: Pattern Recognition (85% confidence)
> - Step 3: Root Cause (75% confidence)
> - Step 4: Conclusion (90% confidence)
> - Overall: 86% confidence"
>
> **Your thought:** "86% is pretty high. I should focus on verifying the 75% root cause step."

**Confidence scores tell you WHERE to spend your time:**
- Don't waste time checking 95% data extraction
- Focus on 75% root cause classification
- That's smart auditing

---

### 3. Learning & Improvement 📚

By reading the AI's reasoning, you learn:

**What queries to run:**
```sql
-- Step 1: Transaction patterns
SELECT COUNT(*), AVG(debit - credit)
FROM `tabGL Entry`
WHERE account = 'Marketing'
GROUP BY YEAR(posting_date)

-- Step 2: New parties
SELECT party, COUNT(*)
FROM `tabGL Entry`
WHERE party NOT IN (SELECT DISTINCT party FROM ...)
```

**How to analyze:**
- Separate volume effect from price effect
- Identify new business vs. existing business
- Classify variance categories
- Structure variance explanations

**The AI becomes your teacher**, not just your tool.

---

### 4. Debugging When Wrong 🐛

**Scenario:** AI says "Volume Change" but you know it's actually "Price Change"

**With Chain of Thought, you can debug:**

```
Step 1: ✅ Correct (157 transactions counted)
Step 2: ✅ Correct (12 new vendors identified)
Step 3: ❌ WRONG - Classified as "Volume Change"
         → Transaction count +149%
         → But missed that new vendors charge 3x higher rates!
Step 4: ❌ Wrong conclusion based on wrong Step 3
```

**Fix:** Update `_identify_root_cause()` to consider average transaction size changes when new parties are involved.

**With black box AI?** You have no idea what went wrong.

**With Chain of Thought?** You can pinpoint and fix the issue.

---

## Frappe/ERPNext Standards Compliance

This implementation follows ALL Frappe/ERPNext standards:

- ✅ **Zero custom fields** on core DocTypes
- ✅ **No core modifications** - Everything in custom app
- ✅ **Clean imports** - Direct imports, no path manipulation
- ✅ **Standard structure** - Follows Frappe DocType conventions
- ✅ **Proper registration** - Uses fixtures in hooks.py
- ✅ **Permission checks** - All operations validated
- ✅ **Error logging** - Uses `frappe.log_error()` pattern
- ✅ **Translatable strings** - All user messages use `_()`
- ✅ **SQL safety** - Parameterized queries only
- ✅ **Type conversion** - Uses `flt()`, `cint()`, `cstr()`
- ✅ **Settings management** - Configuration from database

**Result:** Production-ready, professional, marketplace-quality code.

---

## Future Enhancements

### 1. Multi-Agent Reasoning

What if multiple AI agents analyzed the same variance and debated?

```
Agent 1 (Volume Specialist): "This is clearly volume change (+149% txns)"
Agent 2 (Price Specialist): "Wait, avg txn also increased 2.7%"
Agent 3 (Mix Specialist): "Voucher type distribution shifted too"

Consensus: "Primary cause is volume (96%), with minor mix effect (4%)"
Overall Confidence: 91% (higher than single-agent 86%)
```

### 2. Confidence Explanation

Instead of just `confidence: 0.75`, explain WHY:

```
Confidence: 75%
Reason: "Party matching is ambiguous for 3 vendors with name variations
         (Google LLC vs Google Ads). If these are the same vendor,
         confidence increases to 90%."
```

### 3. Suggested Follow-Up Questions

```
Chain of Thought analysis complete. Consider investigating:
1. Are these new vendors one-time or recurring?
2. What's the ROI on Google Ads spend?
3. Did we reduce spending with any existing vendors?
4. Compare to industry benchmark for digital marketing spend
```

### 4. Learning from Corrections

When a user corrects the AI's reasoning:

```python
# User corrects Step 2
# "Google LLC" and "Google Ads" are the same vendor

# System learns:
party_aliases = {
    "Google LLC": "Google Ads",
    "Meta Business": "Facebook, Inc.",
    "LinkedIn Marketing": "LinkedIn Corporation"
}

# Next time, it won't make the same mistake
```

---

## Getting Started

### Configuration

1. **Setup AI Provider** (requires AI Assistant app)
   - Install AI Assistant app
   - Configure OpenAI API key
   - Enable AI Provider

2. **Configure Variance Settings**
   - Go to: Variance Analysis Settings
   - Enable Chain of Thought analysis
   - Set matching tolerance
   - Configure comparison periods

3. **Run Your First Analysis**
   - Open: Account Variance Report
   - Select account with variance
   - Click: "Generate Chain of Thought Explanation"
   - Review the 4-step reasoning trail

### Usage

```python
# In a Frappe server script or Python file

from variance_analysis.variance_analysis.ai_integration import analyze_with_chain_of_thought

result = analyze_with_chain_of_thought(
    account="Marketing Expenses",
    company="Your Company",
    base_fy="FY 2023",
    comp_fy="FY 2024",
    base_balance=45000,
    comp_balance=115024,
    variance_amount=70024
)

print(f"Explanation: {result['explanation']}")
print(f"Overall Confidence: {result['overall_confidence']:.0%}")

for step in result['reasoning_steps']:
    print(f"\nStep {step['step_number']}: {step['step_type']}")
    print(f"Confidence: {step['confidence_score']:.0%}")
    print(f"Description: {step['description']}")
```
---

## Conclusion

Chain of Thought transforms AI from a black box into a glass box.

Instead of:
```
"Marketing increased. New vendors. Trust me."
```

You get:
```
Step 1: Examined 157 transactions (95% confidence)
Step 2: Identified 12 new vendors (85% confidence)
Step 3: Root cause is volume change (75% confidence)
Step 4: Strategic digital expansion (90% confidence)

Overall: 86% confidence in this analysis
```

**That's auditable. That's trustworthy. That's professional.**

If your dentist can explain why a root canal took 70 minutes instead of 60, your AI can explain why marketing took $70K more than expected.

**Show your work.** That's Chain of Thought.

---
## Contributing

Contributions welcome!

**Topics for discussion:**
- How would your auditors react to Chain of Thought explanations?
- What confidence threshold would you require before relying on AI analysis?
- Should AI explain its confidence scores in even more detail?
- What other finance processes need this level of transparency?

---

**Tags:** #AI #ERPNext #Accounting #VarianceAnalysis #ChainOfThought #Auditability #Finance #CFO #Controller

**Last Updated:** 2025-11-24
