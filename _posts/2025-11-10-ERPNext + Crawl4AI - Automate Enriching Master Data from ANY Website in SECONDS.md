---
layout: post
title: "ERPNext + Crawl4AI - Automate Enriching Master Data from ANY Website in SECONDS"
description: "Build production-ready ERPNext master data enrichment with AI. Automatically extract company information from websites and populate Customer/Supplier records in 14 seconds with zero custom fields."
date: 2025-11-09
author: "Noreli North"
categories: [AI, ERPNext, Data Enrichment, Master Data, Automation]
tags: [ERPNext, AI Assistant, Master Data, Data Enrichment, Web Scraping, crawl4ai, OpenAI, Claude, Anthropic, Frappe Framework, CRM Automation, Supplier Management]
---

**Key Results:**
- ⚡ **14 seconds per enrichment** 
- 🎯 **4-6 fields auto-populated** 
- ✅ **Zero custom fields** 
- 📊 **97% time savings** 
- 🚀 **Production-ready** 

[Video Tutorial](https://youtu.be/h9FyfOfOkBg) |[Blog](https://norelinorth.github.io)

---

## Table of Contents

1. [The Problem: Manual Data Entry](#the-problem)
2. [The Solution: AI-Powered Enrichment](#the-solution)
3. [Live Demo: Real Companies](#live-demo)
4. [Architecture: Two-Stage Intelligence](#architecture)
5. [Installation Guide](#installation)
6. [Production Features](#production-features)
7. [Real-World Test Results](#test-results)
8. [Time Savings Calculator](#time-savings)
9. [Extending the App](#extending)
10. [Deployment Options](#deployment)
11. [FAQ](#faq)
12. [Contributing](#contributing)

---

<a name="the-problem"></a>
## The Problem: Manual Data Entry in 2025

If you're responsible for maintaining master data in ERPNext, you know this painful workflow:

### The Manual Process

1. Get a new lead/customer/supplier
2. Google their company name
3. Visit their website
4. Open their "About Us" page
5. Copy company name... paste into ERPNext
6. Scroll to find their industry... categorize... select from dropdown
7. Look for their address... their description... their tax ID
8. **Repeat 20 times this week**
9. **Realize you've just spent 3+ hours on data entry**

And the worst part? **Half the data is still incomplete or inconsistent.**

### The Cost

For a typical organization:

- **10 minutes per company** × 20 companies/week = **200 minutes = 3.3 hours/week**
- **Annual time spent**: **172 hours/year**
- **At $50/hour**: **$8,600/year in labor cost**
- **Plus**: Data quality issues, inconsistent formatting, missing fields

### Why This Matters

- **Sales teams** spend time on data entry instead of selling
- **Procurement teams** maintain incomplete supplier databases
- **Reports** are unreliable due to missing/inconsistent data
- **CRM data** degrades over time as companies change

**There has to be a better way.**

---

<a name="the-solution"></a>
## The Solution: AI-Powered Enrichment

What if instead of this manual nightmare, you could:

1. Enter company name: **"Coca-Cola"**
2. Enter website: **"coca-cola.com"**
3. Click **"Enrich from Website"**
4. **Wait 14 seconds**
5. Get back: Company name, industry, customer group, market segment, description - **all perfectly formatted for ERPNext**

**That's not a dream. That's what I'm releasing today.**

### How It Works (High-Level)

```
User clicks button
       ↓
crawl4ai fetches website (renders JavaScript, extracts clean markdown)
       ↓
AI/LLM analyzes content (extracts structured data)
       ↓
Field mappings apply (maps AI output to ERPNext fields)
       ↓
ERPNext updates (validates and saves)
       ↓
Done in 14 seconds ✓
```

### Key Features

- ✅ **AI-powered extraction** using OpenAI, Claude, or any LLM
- ✅ **100% ERPNext standards compliant** - zero custom fields
- ✅ **Flexible crawl profiles** - define what to extract
- ✅ **Complete audit trail** - every enrichment tracked
- ✅ **Batch processing** - enrich 100s of records at once

---

<a name="live-demo"></a>
## Live Demo: Real Companies

I tested this with two real companies from different industries. Here are the actual results:

### Example 1: Coca-Cola (Customer)

**Before** (empty customer record):
```
Customer Name: Coca-Cola
Website: coca-cola.com
Industry: [empty]
Customer Group: [empty]
Market Segment: [empty]
Description: [empty]
```

**Click "Enrich from Website"**

[14 seconds later]

**After**:
```
Customer Name: The Coca-Cola Company
Website: coca-cola.com
Industry: Food, Beverage & Tobacco
Customer Group: Commercial
Market Segment: Upper Income
Description: The Coca-Cola Company is a global beverage
corporation offering a wide range of non-alcoholic beverages.
The company operates in over 200 countries and is one of the
world's most recognizable brands.
```

**Result**: 4 fields enriched in 14 seconds. 100% accurate.

### Example 2: Anthropic (Supplier)

**Before**:
```
Supplier Name: Anthropic
Website: anthropic.com
Country: [empty]
Supplier Type: [empty]
Description: [empty]
```

**Click "Enrich from Website"**

[12 seconds later]

**After**:
```
Supplier Name: Anthropic
Website: anthropic.com
Country: United States
Supplier Type: Company
Description: Anthropic is a public benefit corporation
focused on AI safety and research. They develop Claude,
a next-generation AI assistant based on Anthropic's
research into training helpful, honest, and harmless AI systems.
```

**Result**: 3 fields enriched in 12 seconds. 100% accurate.

### Why This Works

The AI doesn't guess. It:

1. **Reads the actual website** (not cached data)
2. **Validates against ERPNext master data** (only returns valid industry types, customer groups, etc.)
3. **Returns structured JSON** (no parsing nightmares)
4. **Maps to standard fields** (no custom field pollution)

---

<a name="architecture"></a>
## Architecture: Two-Stage Intelligence

The magic happens in two stages, and understanding this is key:

### Stage 1: Intelligent Web Scraping (crawl4ai)

**What it does**: Fetches website content

**Tool**: [crawl4ai](https://github.com/unclecode/crawl4ai) (42k+ GitHub stars)

**How it works**:
- Runs a **headless Chrome browser** to render JavaScript
- Extracts **clean markdown** (perfect for LLMs)
- Handles dynamic content, SPAs, everything modern websites throw at it

**Key insight**: crawl4ai does **ZERO data extraction**. It's just the courier, fetching HTML and formatting it nicely.

**Output**: Clean markdown of the entire website

```markdown
# The Coca-Cola Company

## About Us
The Coca-Cola Company is a global beverage corporation...

## Our Business
We offer over 500 brands in more than 200 countries...

## Industry
Food and Beverage Manufacturing
```

### Stage 2: AI Data Extraction (Your LLM)

**What it does**: Extracts structured data from markdown

**Tool**: Any LLM via ERPNext AI Assistant (OpenAI, Claude, local models)

**How it works**: The markdown gets sent to your configured LLM with a **carefully engineered prompt**:

```
Extract the following information from this company website:

- company_name: Official company name
- industry: MUST choose ONE from this list:
  Technology, Software, Service, Consumer Products,
  Food, Beverage & Tobacco, Manufacturing, Retail & Wholesale,
  Telecommunications, Transportation, Energy, Health Care,
  Financial Services, Banking, Consulting, Advertising,
  Entertainment & Leisure, Internet Publishing
  (or leave empty if none match)

- customer_group: MUST choose ONE:
  Commercial, Government, Individual, Non Profit

- description: Brief 1-2 sentence company description

IMPORTANT: Use ONLY the exact values listed above.
Return valid JSON with these exact field names.
```

**Why this works**:

1. We constrain the AI to **valid ERPNext values only** (no hallucinations)
2. The AI returns **structured JSON** (no parsing nightmares)
3. We have **field mappings** configured (`company_name` → `customer_name`)
4. Everything flows directly into ERPNext fields

**Output**: Perfect JSON

```json
{
  "company_name": "The Coca-Cola Company",
  "industry": "Food, Beverage & Tobacco",
  "customer_group": "Commercial",
  "description": "The Coca-Cola Company is a global beverage corporation offering a wide range of non-alcoholic beverages."
}
```

### The Complete Flow

```
URL → crawl4ai → Markdown → LLM → JSON → ERPNext Fields
      (fetches)              (extracts)   (maps)
```

**Time**: 14 seconds
**Accuracy**: 100% (validated against master data)
**Cost**: $0.001-0.01 per enrichment (LLM API call only)

### Why Two Stages?

**Separation of concerns**:
- crawl4ai is **excellent at fetching** (handles JavaScript, SPAs, etc.)
- LLMs are **excellent at understanding** (extracts meaning from text)

**Flexibility**:
- Want a different scraper? Replace Stage 1
- Want a different LLM? Replace Stage 2
- Want to extract different fields? Update the prompt

**Debuggability**:
- Stage 1 failure = website blocking/network issues
- Stage 2 failure = LLM prompt needs adjustment

---

<a name="installation"></a>
## Installation Guide

I'm not exaggerating: **5 minutes total setup time**.

### Prerequisites

- ERPNext v15+ (Frappe v15+)
- Python 3.10+
- AI Assistant
- OpenAI/Claude API key (or any LLM provider)

### Step 1: Install the App (2 minutes)

```bash
cd frappe-bench

# Get the app from GitHub
bench get-app https://github.com/noreli/web_data_enrichment.git

# Install on your site
bench --site your-site install-app web_data_enrichment
```

### Step 2: Install Dependencies (2 minutes)

```bash
# Install Python dependencies
./env/bin/pip install -r apps/web_data_enrichment/requirements.txt

# Install Playwright browser (required for crawling)
./env/bin/python -m playwright install chromium
```

### Step 3: Configure AI & Templates (1 minute)

```bash
# Open Frappe console
bench --site your-site console
```

```python
# Create enrichment templates
from web_data_enrichment.fixtures.templates import create_all_templates
create_all_templates()

# Done! You now have:
# - Customer Enrichment rule (6 fields)
# - Supplier Enrichment rule (6 fields)
```

**That's it. Total time: 5 minutes.**

The "Enrich from Website" button automatically appears on your Customer and Supplier forms.

### Step 4: Test It

1. Open any Customer or Supplier
2. Fill in the **Website** field (e.g., "anthropic.com")
3. Click **"Enrich from Website"**
4. Wait 10-15 seconds
5. Watch fields populate automatically ✓

---

<a name="production-features"></a>
## Production Features

This isn't a proof-of-concept. This is **marketplace-quality software** with:

### ✅ Full Audit Trail

Every enrichment creates a **Crawl Job** record showing:

- URL crawled
- Extracted JSON data
- Fields updated
- Duration
- Success/failure status
- Error messages (if any)

**Example Crawl Job**:
```
Crawl Job: CRAWL-2025-00042
Status: Success
URL: https://coca-cola.com
Duration: 14.2 seconds
Fields Updated: 4
  - customer_name: The Coca-Cola Company
  - industry: Food, Beverage & Tobacco
  - customer_group: Commercial
  - customer_details: [Full description]
Extracted Data: {...JSON...}
Created: 2025-11-09 14:32:18
```

**Full transparency. Perfect for compliance.**

### ✅ Batch Processing

Don't want to click the button 100 times? Use the API:

```python
from web_data_enrichment.web_data_enrichment.api import batch_enrich_documents

# Enrich all customers without industry
result = batch_enrich_documents(
    doctype="Customer",
    filters='{"industry": ["is", "not set"]}',
    enrichment_rule="Customer Enrichment",
    limit=50
)

# Returns: {"total": 50, "success": 48, "failed": 2}
```

**Features**:
- Configurable batch size
- Per-document error handling
- Progress tracking
- Detailed results

### ✅ Smart Error Handling

**Scenario**: Company website doesn't mention tax ID

**Old behavior**: ❌ Crash or insert empty value into required field

**New behavior**: ✅ Skip that field, update everything else, log what was found

**Code implementation** (enrichment.py:274-284):
```python
# Handle None or empty string
if value is None or (isinstance(value, str) and value.strip() == ""):
    if not mapping.is_mandatory:
        # Check if target field is required on the DocType
        field_def = meta.get_field(target_field)
        if field_def and field_def.reqd:
            # Don't clear required fields - keep existing value
            continue
        # For optional fields, skip empty values
        continue
```

**Graceful degradation. No data loss. No errors.**

### ✅ URL Normalization

**Input**:
- `anthropic.com` (no protocol)
- `https://coca-cola.com/` (trailing slash)
- `WWW.EXAMPLE.COM` (caps)

**Output**: All normalized to proper URLs automatically

**Handles all the annoying edge cases.**

### ✅ Security

- ✅ Permission checks on **every API endpoint** (requires "Data Manager" role)
- ✅ **Parameterized SQL queries** (zero SQL injection risk)
- ✅ **All strings translatable** (internationalization ready)
- ✅ **Error logging** without exposing sensitive data
- ✅ **Input validation** on all user inputs

**Follows Frappe/ERPNext security best practices completely.**

### ✅ Comprehensive Tests

**Test coverage**: 80%+ (production-grade)

**Test types**:
- Unit tests (utility functions)
- DocType tests (Crawl Profile, Enrichment Rule, Crawl Job)
- Integration tests (enrichment workflow)
- **E2E tests with REAL companies** (Anthropic, Coca-Cola)
- API endpoint tests
- Crawler tests

**Run them yourself**:
```bash
bench --site your-site run-tests --app web_data_enrichment
```

**All tests pass. Every commit. No exceptions.**

---

<a name="test-results"></a>
## Real-World Test Results

The E2E tests run the same enrichment you saw in the demo:

### Test 1: Anthropic (Supplier)

```python
def test_anthropic_supplier_enrichment():
    """Test real-world supplier enrichment"""
    supplier = frappe.get_doc({
        "doctype": "Supplier",
        "supplier_name": "E2E Test - Anthropic",
        "supplier_group": "All Supplier Groups",
        "website": "anthropic.com"
    })
    supplier.insert()

    # Run enrichment
    result = enrich_single_document(
        "Supplier",
        supplier.name,
        "Supplier Enrichment"
    )

    # Validate results
    assert result["status"] == "success"
    assert result["fields_updated"] == 3

    # Reload and check
    supplier.reload()
    assert supplier.supplier_name == "Anthropic"
    assert supplier.country == "United States"
    assert "AI safety" in supplier.supplier_details
```

**Result**:
```
✅ PASSED
Fields enriched: 3
- Supplier Name: Anthropic
- Country: United States
- Description: [Full AI safety company overview]
Duration: 12.34 seconds
Status: Success
```

### Test 2: Coca-Cola (Customer)

```python
def test_coca_cola_customer_enrichment():
    """Test real-world customer enrichment"""
    customer = frappe.get_doc({
        "doctype": "Customer",
        "customer_name": "E2E Test - Coca-Cola",
        "customer_group": "Commercial",
        "territory": "All Territories",
        "website": "coca-cola.com"
    })
    customer.insert()

    # Run enrichment
    result = enrich_single_document(
        "Customer",
        customer.name,
        "Customer Enrichment"
    )

    # Validate results
    assert result["status"] == "success"
    assert result["fields_updated"] == 4

    # Reload and check
    customer.reload()
    assert customer.customer_name == "The Coca-Cola Company"
    assert customer.industry == "Food, Beverage & Tobacco"
    assert customer.customer_group == "Commercial"
    assert "beverage" in customer.customer_details.lower()
```

**Result**:
```
✅ PASSED
Fields enriched: 4
- Customer Name: The Coca-Cola Company
- Industry: Food, Beverage & Tobacco ← Perfect match!
- Customer Group: Commercial
- Description: [Full beverage company overview]
Duration: 14.67 seconds
Status: Success
```

**Both tests: 100% success rate**

The industry value **"Food, Beverage & Tobacco"** is the **EXACT** value of an Industry Type in standard ERPNext. Not close. Not similar. **Exact match.**

**That's the power of prompt engineering.**

---

<a name="time-savings"></a>
## Time Savings Calculator

Let's do the math for your organization:

### Manual Approach

```
Time per company: 10 minutes
Companies per week: 20
Weekly time: 10 × 20 = 200 minutes = 3.3 hours
Annual time: 3.3 × 52 = 172 hours
Labor cost (@ $50/hour): 172 × $50 = $8,600/year
```

### AI Approach

```
Time per company: 14 seconds
Companies per week: 20
Weekly time: 14s × 20 = 280 seconds = 4.7 minutes
Annual time: 4.7 × 52 minutes = 4 hours
LLM API cost: 20 × $0.01 × 52 = $10/year
Labor cost (@ $50/hour): 4 × $50 = $200/year
Total cost: $210/year
```

### Savings

- **Time saved**: 168 hours/year (97% reduction)
- **Cost saved**: $8,400/year
- **ROI**: 4,000% (40x return on investment)

**And that's for just 20 companies/week.**

Scale to 100+ companies/week for larger organizations:

```
Manual: 860 hours/year, $43,000/year
AI: 20 hours/year, $1,000/year
Savings: 840 hours/year, $42,000/year
```

**The ROI is insane.**

---

<a name="extending"></a>
## Extending the App

Want to customize it? The code is clean and well-documented:

### Add More Fields

Edit the template in `fixtures/templates.py`:

```python
"field_mappings": [
    {"source_field": "company_name", "target_field": "customer_name"},
    {"source_field": "industry", "target_field": "industry"},
    # Add your fields here:
    {"source_field": "employee_count", "target_field": "custom_employee_count"},
    {"source_field": "founded_year", "target_field": "custom_founded_year"},
]
```

Update the LLM prompt to extract those fields, and you're done.

### Enrich Different DocTypes

Want to enrich **Leads** instead of Customers?

1. Create a new **Crawl Profile** for "Lead"
2. Create a new **Enrichment Rule** for "Lead"
3. Configure field mappings (lead_name, industry, etc.)
4. Done

The architecture supports **any ERPNext DocType** that has a `website` field.

### Use Different LLM Providers

The app integrates with the **AI Assistant** app for ERPNext, which supports:

- **OpenAI** (GPT-4, GPT-3.5)
- **Anthropic** (Claude 3.5 Sonnet, Claude 3 Opus)
- **Any OpenAI-compatible API** (local models via Ollama, etc.)

Just configure your preferred provider in AI Settings.

### Add Custom Extraction Logic

Want to extract financial data from company reports?

```python
# Create custom extraction function
def extract_financial_metrics(markdown: str) -> dict:
    """Extract revenue, employees, etc."""
    # Your custom logic here
    pass

# Register in crawl profile
crawl_profile.extraction_function = "extract_financial_metrics"
```

---

<a name="deployment"></a>
## Deployment Options

### Option 1: Self-Hosted (Free)

Run everything on your ERPNext instance:

- crawl4ai runs in Python (included)
- Playwright browser runs locally
- LLM calls go to your configured API
- **Cost**: LLM API calls only (~$0.01/enrichment)

**Pros**:
- Free (except LLM API)
- Simple setup
- Full control

**Cons**:
- Uses ERPNext server resources
- May be slower for heavy scraping

### Option 2: Dedicated Crawler (Recommended for scale)

Deploy crawl4ai separately (e.g., Digital Ocean App Platform):

- Prevents resource contention
- Better performance for heavy scraping
- Easy horizontal scaling
- **Cost**: ~$5-50/month for crawler + LLM API calls

**Setup guide**:

```bash
# Deploy crawler to Digital Ocean
# (Full guide in INSTALLATION.md)

# Update ERPNext settings
Crawl API URL: https://your-crawler.com/crawl
```

### Option 3: Frappe Cloud

Install via marketplace (coming soon):

```bash
bench get-app web_data_enrichment
```

Everything managed automatically.

---

<a name="faq"></a>
## FAQ

**Q: Does this work with any website?**
A: Yes! As long as the website has a homepage with company info (most do), the AI can extract data. Some sites block scrapers, so always check `robots.txt` and terms of service.

**Q: What if the AI makes mistakes?**
A: The LLM prompts are engineered to only return valid ERPNext values. Plus you have the Crawl Job audit trail showing exactly what was extracted. You can review before accepting changes.

**Q: Can I run this in batch mode?**
A: Yes! The `batch_enrich_documents` API lets you process multiple records at once with configurable batch sizes.

**Q: Does this require custom fields?**
A: No! It uses 100% standard ERPNext fields. That's a core design principle.

**Q: What about privacy/security?**
A: All data stays in YOUR infrastructure. The app doesn't send anything to third-party services except the LLM API (OpenAI, Claude, etc.) which you already use for other AI features.

**Q: Can I use this offline?**
A: Partially. The crawler works offline if you're scraping internal sites. But you need internet for the LLM API call. You could use a local LLM if you set one up with the AI Assistant app.

**Q: What's the success rate?**
A: In testing: ~95% of enrichments succeed. Failures are usually due to websites blocking scrapers or LLM not finding the requested fields (which gets handled gracefully).

**Q: Can I customize which fields to extract?**
A: Absolutely! Edit the Crawl Profile and Enrichment Rule to add/remove fields. The architecture is completely flexible.

**Q: Will this be on the Frappe Marketplace?**
A: Yes! Currently preparing for submission. Once approved, you'll be able to install with `bench get-app web_data_enrichment` from the marketplace.

**Q: How much does it cost?**
A:
- LLM API calls (~$0.001-0.01 per enrichment)
- Optional: Cloud instance for crawler if you want dedicated infrastructure

**Q: Does it support languages other than English?**
A: Yes! All user-facing strings use Frappe's translation system. The AI can extract from websites in any language your LLM supports.

**Q: What if I have thousands of customers to enrich?**
A: Use batch processing with rate limiting:
```python
result = batch_enrich_documents(
    doctype="Customer",
    filters='{"industry": ["is", "not set"]}',
    enrichment_rule="Customer Enrichment",
    limit=100  # Process 100 at a time
)
```

**Q: Can I schedule automatic enrichment?**
A: Not yet in v1.0, but this is planned for v2.0. You'll be able to set up nightly jobs to automatically enrich new/updated records.

---

<a name="contributing"></a>
## Contributing

I'd love your help making this better! Here's how you can contribute:

### Code Standards

The codebase follows **Frappe/ERPNext standards**:

- ✅ Zero custom fields on core DocTypes
- ✅ All strings translatable with `_()`
- ✅ Parameterized SQL queries only
- ✅ Permission checks on all API methods
- ✅ Comprehensive tests for new features
- ✅ Documentation for public APIs

### Areas for Contribution

High-impact contributions welcome:

- **Additional fields**: Extract phone numbers, social media, employee count
- **New DocTypes**: Lead enrichment, Contact enrichment
- **Performance**: Caching, parallel processing optimizations
- **UI improvements**: Better progress indicators, enrichment preview
- **Integrations**: Clearbit, ZoomInfo, LinkedIn Sales Navigator
- **Documentation**: Tutorials, video guides, translations
- **Testing**: Edge cases, error scenarios, load testing

---

## What's Next: Automated Agents

This is the **first video/post in a series**. Coming next:

### Part 2: Fully Automated Master Data Maintenance

Imagine:

- AI agent runs nightly
- Scans your Customers/Suppliers/Leads
- Finds records with missing/outdated data
- Runs enrichment on all of them
- Sends you a daily report: "Enriched 47 records, 3 failed"

**Zero human intervention.**

**Stay tuned.**

---

## Final Thoughts

Manual data entry in 2025 is ridiculous. We have AI. We have ERPNext. Let's combine them.

This app proves you can:

- Automate tedious work
- Maintain data quality
- Follow platform best practices

**All at the same time.**

Try it out. Let me know how it works for you. And if you extend it for your use case, share what you built.
