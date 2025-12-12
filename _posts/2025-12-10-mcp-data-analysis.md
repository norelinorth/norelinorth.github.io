---
layout: post
title: "Turn Claude into Your ERPNext Business Analyst with MCP"
date: 2025-12-10
categories: [erpnext, ai, mcp, business-intelligence]
tags: [claude, erpnext, mcp, frappe, data-analysis, artificial-intelligence]
author: Noreli North
description: "Connect Claude AI directly to your ERPNext data using MCP. Get real-time business insights in seconds - no CSV exports, no hallucinations, just grounded AI analysis."
image: /assets/MCP_Data Analysis.png
---

**Key Results:**
- ✅ **Real-time data**
- 🎯 **Deeper insights**
- 📋 **Trustworthy answers**
- 🔍 **Cost-effective**
  

[Video Demo](https://youtu.be/D7xKSdKc9L4) | [Documentation](https://norelinorth.github.io)

---
# Turn Claude into Your ERPNext Business Analyst

Let's be honest. AI models like Claude are **incredible** at analyzing data. They can spot trends, write summaries, and give you strategic advice that would cost thousands from a consultant.

But they have one **massive** problem.

**They don't know YOUR business.**

Ask Claude right now: "How were my sales last month in ERPNext?" It can't answer. It has no idea who your customers are, what products you sell, or what your invoices look like.

It either says "I don't have access to that information" - or worse - it **makes up numbers**. Hallucinations.

So here's the question: What if you could give Claude **direct, secure access** to your ERPNext data?

That's exactly what I built. Let me show you how it works.

---

## The Problem with Traditional BI

Right now, most ERPNext users have two options for business intelligence:

### Option 1: Export CSVs, Upload to ChatGPT

It's slow. It's manual. And the data is **stale** the moment you export it. You're always working with yesterday's numbers.

### Option 2: Pay for Expensive BI Tools

We're talking about a lot of money per month for tools that *still* require you to configure dashboards, write queries, and often need a consultant to set up.

### The Real Problem Nobody Talks About

When you use a raw LLM with no data connection - when you just paste text into ChatGPT - you lose **all context**. It doesn't know your customer history. It doesn't understand your product mix. It can't cross-reference invoices with inventory.

And when it doesn't have data... it makes things up. Confidently.

---

## Enter MCP: The Bridge Between AI and Your Data

**MCP** stands for **Model Context Protocol**. It's the standard that lets AI models like Claude securely connect to external data sources.

Think of it as a bridge:
- **One side**: Claude - the brain, the reasoning engine
- **Other side**: ERPNext - your business data, your memory

MCP is what connects them.

I've built an MCP Server specifically for ERPNext. Here's what it exposes:

### Six Core Business Entities

| Entity | What It Contains | Example Query |
|--------|------------------|---------------|
| **Customers** | Buyer relationships, credit limits, transaction history | "List customers who haven't ordered in 60 days" |
| **Suppliers** | Procurement network, lead times, payment terms | "Which suppliers have the longest delivery times?" |
| **Items** | Inventory, pricing, stock levels, categories | "Show items with stock below reorder level" |
| **Sales Invoices** | Revenue transactions - who bought what, when, for how much | "What's my average invoice value this quarter?" |
| **Purchase Invoices** | Cost transactions - what you're spending and with whom | "Break down my costs by expense category" |
| **Companies** | Multi-entity setups for subsidiary analysis | "Compare revenue across our three subsidiaries" |

With these six data points, Claude can answer almost **any** business question you have.

---

## Real Examples: Cross-Entity Analysis

Here's where it gets really powerful.

Because Claude has access to **multiple** data sources, it can do cross-entity analysis. This is the kind of stuff that normally requires SQL joins and custom reports.

### Example 1: Profit Margin Analysis

**Query:**
```
Looking at my Sales Invoices and Items together - which products have
the highest profit margin, and who are the suppliers for those products?
```

**What happens:** Claude pulls Sales Invoices for revenue data, cross-references Items for cost data, calculates margins, AND looks up Supplier relationships.

**Result:** "Product XYZ - 42% margin - Supplied by ABC Corp"

One question. Multiple DocTypes. Instant analysis.

### Example 2: Payment Pattern Analysis

**Query:**
```
Analyze my customer payment patterns. Group customers into three categories:
those who pay within 15 days, 15-30 days, and beyond 30 days.
Which category has the highest total revenue?
```

This is accounts receivable analysis - something that usually requires a specialized report or BI tool - just from a natural language question.

### Example 3: Inventory Planning

**Query:**
```
Based on sales velocity from the last 6 months, which 5 items should I
prioritize for restock? Consider both unit volume and revenue contribution.
```

It's not just looking at what sold the most. It's weighing volume against revenue. It's thinking like a **buyer**.

---

## No More Hallucinations

Here's the beautiful thing about MCP - it **grounds** the AI in real data.

When Claude answers through MCP, it's citing your actual ERPNext documents:
- Invoice numbers
- Customer IDs
- Exact amounts with decimals

If the data isn't in your system... Claude tells you it's not there. It doesn't guess. It doesn't hallucinate.

**Example:**

Query: "What was the total revenue from our Singapore subsidiary in Q3 2024?"

Response: "I don't find a Singapore subsidiary in your company data" or "There are no invoices matching those criteria."

**That** is the difference. Grounded AI. Trustworthy answers.

---

## Security Built In

I know what you're thinking - "What about security?"

This MCP server includes:

- **API key authentication** - Every request requires valid credentials
- **Audit logging** - Full trail of every query
- **Configurable access** - Control exactly which DocTypes and fields are exposed
- **Role-based permissions** - Leverage ERPNext's existing permission system

This isn't a wild west connection. It's controlled, secure, and auditable. Exactly what you need for business data.

---

## Getting Started

### Prerequisites

- ERPNext v14 or v15
- Claude Desktop or Claude Code
- 10 minutes for setup

### Quick Installation

```bash
# Get the app
bench get-app https://github.com/YOUR_REPO/mcp-erpnext

# Install on your site
bench --site your-site.local install-app mcp

# Configure API access
bench --site your-site.local set-config mcp_enabled true
```

### Connect Claude

1. Open Claude Desktop settings
2. Add MCP server configuration:

```json
{
  "mcpServers": {
    "erpnext": {
      "url": "https://your-erpnext-site.com/api/method/mcp.endpoint",
      "apiKey": "your-api-key"
    }
  }
}
```

3. Restart Claude Desktop
4. Start asking questions!

---

## Query Cheat Sheet

### Customer Analysis
```
Who are my top 10 customers by total revenue?
List customers who haven't ordered in 60 days
Show customers ranked by average order value
```

### Sales Analysis
```
What's our month-over-month revenue growth?
Which invoices are overdue beyond 30 days?
Compare this month's sales to last month
```

### Inventory Analysis
```
What are our top 10 best-selling items?
Which items have the highest profit margin?
Show items below reorder point
```

### Cross-Entity Analysis
```
For our top-selling items, who are the suppliers?
Which customers buy our premium products?
Give me an executive summary: top customers, top products,
revenue trends, and any concerns for the quarter
```

### Tips for Better Queries

| Instead of... | Try... |
|--------------|--------|
| "Show me sales" | "Show me total sales revenue for Q4 2024 by customer" |
| "Who are my top customers?" | "Who are my top 5 customers by revenue in the last 90 days?" |
| "How are my sales?" | "Compare this month's sales to the same month last year" |
| "List all invoices" | "Analyze invoice trends - are order values increasing or decreasing?" |

---

## What You Get

You're essentially getting a data analyst who:

- **Never sleeps** - Available 24/7
- **Knows your entire database** - All six core entities
- **Responds in seconds** - No waiting for reports
- **Costs a fraction** - No expensive BI tools or consultants

That query that would take 30 minutes in Excel? Export CSV, clean it up, build pivot tables...

**8 seconds.** Real data. Zero exports.

---

## Architecture Overview

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│                 │     │                 │     │                 │
│  Claude AI      │◄───►│  MCP Protocol   │◄───►│  ERPNext        │
│  (Reasoning)    │     │  (Bridge)       │     │  (Your Data)    │
│                 │     │                 │     │                 │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
        │                       │                       │
   Natural Language      Secure API Calls         Live Database
      Queries             + Audit Logs              Queries
```

The MCP server:
1. Receives natural language queries from Claude
2. Translates them to ERPNext API calls
3. Returns structured data with document references
4. Logs everything for audit trails

---

## What's Next?

This is just the beginning. Coming soon:

1. **Custom Tool Development** - Build your own MCP tools
2. **Advanced Security Configurations** - Fine-grained access control
3. **Automated Reports** - Scheduled queries and dashboards
4. **Multi-Company Analysis** - Deep dive into intercompany data

---

## Try It Yourself

The best way to understand the power of MCP + ERPNext is to try it yourself.

1. Clone the repo
2. Install on your ERPNext instance
3. Connect Claude
4. Ask your first business question

What queries would **you** want to ask your ERPNext system?

Drop a comment below - I might feature them in the next post.

---

*This is part of a series on AI-powered business intelligence for ERPNext. Subscribe to stay updated on new posts about MCP, Claude, and ERPNext automation.*

---

## Resources

- [MCP Protocol Documentation](https://modelcontextprotocol.io)
- [ERPNext Documentation](https://docs.erpnext.com)
- [Frappe Framework](https://frappeframework.com)
- [Claude API Documentation](https://docs.anthropic.com)

![alt text](/assets/MCP_Data Analysis.png) 
