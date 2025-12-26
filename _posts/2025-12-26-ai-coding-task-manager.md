---
layout: post
title: "Building the Complete AI Integration Layer for ERPNext: MCP Server Deep Dive"
date: 2025-12-26
categories: [erpnext, ai, mcp, architecture, rag]
tags: [claude, erpnext, mcp, frappe, langchain, semantic-search, knowledge-base, api, developer-tools]
author: Noreli North
description: "A comprehensive technical guide to the MCP Server for ERPNext - from AI task management and semantic search to business intelligence tools and enterprise-grade security. Learn how to build a production-ready AI integration layer."
image: /assets/images/AI Coding Team.png
---

**Key Results:**
- ⚡ **15+ AI-accessible tools** (query, analyze, track, search)
- 🛡️ **Enterprise security** (API keys, rate limiting, IP filtering, audit logs)
- 🎯 **Hybrid RAG search** (keyword + semantic with Reciprocal Rank Fusion)
- ✅ **100% standards compliant** (no core modifications)
- 🤖 **AI task management** (Kanban boards, Gantt charts, execution tracking)
- 📊 **Production-tested** (complete audit trails, comprehensive test coverage)

[Video Demo]() | [Documentation](https://norelinorth.github.io)

---
![alt text](/assets/AI Coding Team.png)

When I first introduced MCP (Model Context Protocol) integration for ERPNext, I showed how AI could track its own work on a Kanban board. That was just the beginning.

Today, MCP Server for ERPNext has evolved into a **complete AI integration layer** - a production-ready framework that transforms ERPNext into an AI-accessible enterprise system with task management, knowledge retrieval, business intelligence, and enterprise-grade security.

This deep dive covers everything: architecture, implementation details, and the techniques that make it work.

---

## Table of Contents

1. [What I Built](#what-i-built)
2. [Architecture Overview](#architecture-overview)
3. [The Protocol: JSON-RPC 2.0](#the-protocol-json-rpc-20)
4. [Tool System Deep Dive](#tool-system-deep-dive)
5. [Task Management System](#task-management-system)
6. [Knowledge Base & RAG](#knowledge-base--rag)
7. [Business Intelligence Tools](#business-intelligence-tools)
8. [Security Architecture](#security-architecture)
9. [Performance & Caching](#performance--caching)
10. [Installation & Configuration](#installation--configuration)
11. [Real-World Usage Patterns](#real-world-usage-patterns)
12. [What's Next](#whats-next)

---

## What I Built

MCP Server for ERPNext is not just an API wrapper. It's a comprehensive AI integration framework with five major subsystems:

| Subsystem | What It Does |
|-----------|--------------|
| **Protocol Layer** | JSON-RPC 2.0 compliant MCP endpoint with authentication |
| **Task Management** | Projects, tasks, Kanban boards, Gantt charts, execution tracking |
| **Knowledge Base** | Document indexing, chunking, hybrid search (keyword + semantic) |
| **Analysis Tools** | Pre-built business intelligence queries with caching |
| **Security Layer** | API keys, rate limiting, IP filtering, comprehensive audit logging |

**Key Metrics**:
- **94 configuration fields** across 11 settings sections
- **11 DocTypes** for data management
- **15+ built-in tools** for AI agents
- **6 pre-built analysis tools** for business intelligence
- **100% Frappe/ERPNext standards compliant**

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        AI Agent (Claude, etc.)                  │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     MCP Protocol Layer                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │   Auth      │  │  JSON-RPC   │  │   Rate Limiting         │  │
│  │   (API Key) │  │  Endpoint   │  │   (Redis)               │  │
│  └─────────────┘  └─────────────┘  └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        Tool Registry                            │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐    │
│  │  Task      │ │  Analysis  │ │  Knowledge │ │  Dev       │    │
│  │  Tools     │ │  Tools     │ │  Tools     │ │  Tools     │    │
│  └────────────┘ └────────────┘ └────────────┘ └────────────┘    │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     ERPNext / Frappe                            │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐    │
│  │  DocTypes  │ │  Reports   │ │  Database  │ │  Files     │    │
│  └────────────┘ └────────────┘ └────────────┘ └────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### Core Design Principles

1. **Standard Frappe Patterns Only**: No core modifications, no custom fields on standard DocTypes
2. **Permission Inheritance**: All operations respect Frappe's role-based permissions
3. **Fail-Safe Security**: Multiple layers of authentication, authorization, and rate limiting
4. **AI-Friendly Responses**: Structured JSON responses optimized for LLM consumption

---

## The Protocol: JSON-RPC 2.0

The MCP endpoint implements the [Model Context Protocol](https://modelcontextprotocol.io) specification using JSON-RPC 2.0.

### Endpoint

```
POST /api/method/mcp.mcp.api.protocol.mcp_endpoint
Authorization: Bearer <api-key>
Content-Type: application/json
```

### Request Format

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "id": 1,
  "params": {
    "name": "query_doctype",
    "arguments": {
      "doctype": "Sales Invoice",
      "filters": {"docstatus": 1},
      "fields": ["name", "customer", "grand_total"],
      "limit": 10
    }
  }
}
```

### Supported Methods

| Method | Description |
|--------|-------------|
| `tools/list` | List all available tools with schemas |
| `tools/call` | Execute a tool with arguments |
| `resources/list` | List available data sources |
| `resources/read` | Read resource content |

### Error Handling

The endpoint uses standard JSON-RPC error codes:

```python
ERROR_CODES = {
    -32700: "Parse error",
    -32600: "Invalid Request",
    -32601: "Method not found",
    -32602: "Invalid params",
    -32603: "Internal error",
    -32001: "Authentication required",
    -32002: "Permission denied",
    -32003: "Rate limit exceeded"
}
```

### Implementation Details

The protocol handler (`mcp/mcp/api/protocol.py`) does several things:

```python
@frappe.whitelist(allow_guest=True)
def mcp_endpoint():
    """Main MCP endpoint - JSON-RPC 2.0 compliant."""

    # GET requests = health check (no auth needed)
    if frappe.request.method == "GET":
        return {"status": "ok", "version": "1.2.1"}

    # POST requests require authentication
    api_key = get_bearer_token()
    if not api_key:
        return json_rpc_error(-32001, "Authentication required")
```

---

## Tool System Deep Dive

Tools are the heart of MCP. Each tool is a discrete function that AI agents can discover and execute.

### Tool Registration

Tools are registered in the tool registry with JSON Schema definitions:

```python
# mcp/mcp/api/tools.py

TOOL_REGISTRY = {
    "query_doctype": {
        "name": "query_doctype",
        "description": "Query any exposed ERPNext DocType",
        "inputSchema": {
            "type": "object",
            "properties": {
                "doctype": {"type": "string", "description": "DocType name"},
                "filters": {"type": "object", "description": "Query filters"},
                "fields": {"type": "array", "description": "Fields to return"},
                "limit": {"type": "integer", "default": 20}
            },
            "required": ["doctype"]
        },
        "handler": "mcp.mcp.api.tools.query_doctype"
    }
}
```

### Built-in Tools

#### Data Query Tools

| Tool | Purpose |
|------|---------|
| `query_doctype` | Query any exposed DocType with filters |
| `create_document` | Create new documents |
| `get_report` | Execute ERPNext reports |
| `query_child_table` | Query child tables with parent JOINs |

#### Task Management Tools

| Tool | Purpose |
|------|---------|
| `create_project` | Create Agent Project |
| `create_task` | Create Agent Task with scheduling |
| `update_task_status` | Change task status |
| `complete_task` | Mark task done with output |
| `add_task_step` | Log execution steps |
| `get_project_tasks` | Retrieve project tasks |

#### Analysis Tools

| Tool | Purpose |
|------|---------|
| `analyze_item_sales` | Top items by revenue, quantity, customer reach |
| `analyze_customer_behavior` | Churn risk analysis |
| `analyze_payment_patterns` | AR aging buckets |
| `analyze_supplier_performance` | Supplier spend analysis |
| `analyze_inventory_health` | Stock status and alerts |

#### Knowledge Tools

| Tool | Purpose |
|------|---------|
| `index_document` | Index file for semantic search |
| `search_knowledge` | Hybrid keyword + semantic search |
| `get_document` | Retrieve document content |
| `get_context` | Get relevant context for prompts |

#### Development Tools

| Tool | Purpose |
|------|---------|
| `read_doctype_schema` | Get DocType field definitions |
| `search_code` | Search app source code |
| `run_bench_command` | Execute bench commands |
| `list_installed_apps` | List Frappe apps |

### Custom Tools

You can define custom tools without writing code using the `MCP Custom Tool` DocType:

```json
{
  "tool_name": "get_outstanding_invoices",
  "category": "Query",
  "description": "Get all outstanding sales invoices",
  "python_method": "my_app.tools.get_outstanding_invoices",
  "input_schema": {
    "type": "object",
    "properties": {
      "customer": {"type": "string"},
      "min_amount": {"type": "number"}
    }
  },
  "required_role": "Accounts User",
  "cache_results": true,
  "cache_ttl": 300
}
```

---

## Task Management System

The task management system provides a complete project tracking solution designed for AI agents.

### DocType Hierarchy

```
Agent Project
    │
    ├── Agent Task
    │       │
    │       ├── Agent Task Step (child table)
    │       └── Agent Task Depends On (child table)
    │
    └── Agent Task
            └── ...
```

### Task Status Workflow

```
┌─────────┐     ┌─────────────┐     ┌─────────┐     ┌──────┐
│ To Do   │────▶│ In Progress │────▶│ Testing │────▶│ Done │
└─────────┘     └─────────────┘     └─────────┘     └──────┘
     │                │                   
     │                │                   
     ▼                ▼                   
┌─────────┐     ┌─────────┐
│ Blocked │     │ Failed  │
└─────────┘     └─────────┘
```

### Task Fields

The `Agent Task` DocType captures comprehensive metadata:

```python
# Core fields
title          # Task title
description    # Detailed description
project        # Link to Agent Project
priority       # Low, Medium, High, Urgent
task_type      # Feature, Bug Fix, Research, Documentation, Testing, Other
status         # To Do, In Progress, Testing, Done, Blocked, Failed
assigned_to    # Link to User

# Scheduling
expected_start_date
expected_end_date
progress_percentage  # 0-100

# Execution tracking
estimated_time       # Hours
actual_time         # Hours
started_at          # Datetime
completed_at        # Datetime

# Dependencies
depends_on          # Child table of task dependencies

# Output
output_summary      # Completion summary
error_message       # If failed
affected_files      # Files modified (text list)

# AI Metadata
created_by_ai       # Boolean flag
ai_conversation_id  # Link to conversation
```

### Kanban Board Styling

Tasks appear on a Kanban board with priority-based neon styling:

```javascript
// agent_task_list.js

const PRIORITY_COLORS = {
    'Low': {
        gradient: 'linear-gradient(135deg, rgba(0,217,255,0.15), rgba(0,217,255,0.05))',
        border: 'rgba(0,217,255,0.6)',
        glow: '0 0 15px rgba(0,217,255,0.3)'
    },

```

### Task Tool Implementation

```python
# mcp/mcp/tools/task_tools.py

def create_task(
    title: str,
    project: str = None,
    description: str = None,
    priority: str = "Medium",
    task_type: str = "Feature",
    expected_start_date: str = None,
    expected_end_date: str = None,
    estimated_time: float = None,
    depends_on: list = None
) -> dict:
    """Create a new Agent Task."""

```

---

## Knowledge Base & RAG

The knowledge base system enables Retrieval-Augmented Generation (RAG) by indexing documents and providing semantic search.

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Document Processing                        │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────────────┐     │
│  │ PDF     │  │ DOCX    │  │ Markdown│  │ Web Crawling    │     │
│  │ Extract │  │ Extract │  │ Extract │  │ (crawl4ai)      │     │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────────┬────────┘     │
│       └────────────┴────────────┴────────────────┘              │
│                              │                                  │
│                              ▼                                  │
│                      ┌──────────────┐                           │
│                      │   Chunker    │                           │
│                      │ (500 tokens) │                           │
│                      └──────┬───────┘                           │
│                             │                                   │
│              ┌──────────────┼──────────────┐                    │
│              ▼              ▼              ▼                    │
│     ┌────────────┐  ┌────────────┐  ┌────────────┐              │
│     │  FTS5      │  │  Embedding │  │  Metadata  │              │
│     │  Index     │  │  (OpenAI)  │  │  Storage   │              │
│     └────────────┘  └────────────┘  └────────────┘              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                       Hybrid Search                             │
│  ┌─────────────────┐              ┌─────────────────┐           │
│  │ Keyword Search  │              │ Semantic Search │           │
│  │ (FTS5)          │              │ (Cosine Sim)    │           │
│  └────────┬────────┘              └────────┬────────┘           │
│           │                                │                    │
│           └────────────┬───────────────────┘                    │
│                        ▼                                        │
│              ┌─────────────────┐                                │
│              │ Reciprocal Rank │                                │
│              │ Fusion (RRF)    │                                │
│              └─────────────────┘                                │
└─────────────────────────────────────────────────────────────────┘
```

### Document Processing Pipeline

```python
# mcp/mcp/knowledge/processor.py

def process_document(doc_name: str) -> dict:
    """Process a document for knowledge base indexing."""

    doc = frappe.get_doc("MCP Knowledge Document", doc_name)

    # 1. Extract text based on source type
    if doc.source_type == "PDF":
        text = extract_pdf(doc.file_path)
    elif doc.source_type == "Web Crawling":
        text = crawl_website(doc.source_url, doc.max_pages, doc.max_depth)
    else:
        text = extract_text(doc.file_path)

    # 2. Chunk the text
    chunks = chunk_text(
        text,
        chunk_size=settings.chunk_size,  # Default: 500 tokens
        overlap=settings.chunk_overlap    # Default: 50 tokens
    )

```

### Hybrid Search Implementation

The hybrid search combines keyword search (fast, exact matching) with semantic search (understanding meaning):

```python
# mcp/mcp/knowledge/hybrid_search.py

def hybrid_search(query: str, limit: int = 10) -> list:
    """
    Perform hybrid search using Reciprocal Rank Fusion.

    Combines:
    - FTS5 keyword search (fast, exact matching)
    - Semantic search (meaning-based)
    """

    # Get settings
    semantic_weight = settings.semantic_weight  # Default: 0.5

    # 1. Keyword search using FTS5
    keyword_results = fts5_search(query, limit=limit * 2)

    # 2. Semantic search using embeddings
    if settings.enable_semantic_search:
        query_embedding = generate_embedding(query)
        semantic_results = vector_search(query_embedding, limit=limit * 2)
    else:
        semantic_results = []

    # 3. Reciprocal Rank Fusion
    combined = reciprocal_rank_fusion(
        keyword_results,
        semantic_results,
        k=60,  # RRF constant
        keyword_weight=1 - semantic_weight,
        semantic_weight=semantic_weight
    )

    return combined[:limit]


def reciprocal_rank_fusion(
    keyword_results: list,
    semantic_results: list,
    k: int = 60,
    keyword_weight: float = 0.5,
    semantic_weight: float = 0.5
) -> list:
    """
    Combine rankings using Reciprocal Rank Fusion.

    RRF Score = sum(1 / (k + rank)) for each result list

    This method is robust to different score scales and
    works well when combining heterogeneous retrieval methods.
    """

    scores = {}

    # Score from keyword search
    for rank, result in enumerate(keyword_results):
        doc_id = result["name"]
        scores[doc_id] = scores.get(doc_id, 0) + keyword_weight / (k + rank + 1)

    # Score from semantic search
    for rank, result in enumerate(semantic_results):
        doc_id = result["name"]
        scores[doc_id] = scores.get(doc_id, 0) + semantic_weight / (k + rank + 1)

    # Sort by combined score
    sorted_results = sorted(scores.items(), key=lambda x: x[1], reverse=True)

    return [{"name": doc_id, "score": score} for doc_id, score in sorted_results]
```

### Deep Web Crawling

The knowledge base can crawl websites to ingest documentation:

```python
# mcp/mcp/knowledge/deep_crawler.py

async def deep_crawl(
    start_url: str,
    max_pages: int = 50,
    max_depth: int = 3,
    keyword_priorities: list = None,
    url_patterns: list = None
) -> list:
    """
    Deep crawl a website using crawl4ai with BFS strategy.

    Features:
    - Breadth-first crawling
    - Keyword prioritization (URLs with keywords crawled first)
    - URL filtering (glob/regex patterns)
    - Code block preservation
    - Rate limiting
    """
```

### Knowledge Tool Usage

```python
# Search knowledge base
result = search_knowledge(
    query="How do I create a custom DocType?",
    limit=5,
    source_type="all"  # or "pdf", "web", "markdown"
)

# Get context for RAG
context = get_context(
    query="Explain the Sales Invoice workflow",
    max_tokens=2000,
    include_metadata=True
)
```

---

## Business Intelligence Tools

Six pre-built analysis tools provide instant business intelligence:

### 1. Item Sales Analysis

```python
# mcp/mcp/tools/analysis_tools.py

@cached_analysis(ttl=900)  # 15-minute cache
def analyze_item_sales(
    from_date: str = None,
    to_date: str = None,
    company: str = None,
    limit: int = 20
) -> dict:
    """
    Analyze top-selling items with multiple metrics.

    Returns:
    - Top items by revenue
    - Top items by quantity
    - Top items by customer reach
    - Revenue trends
    """
```

### 2. Customer Churn Analysis

```python
@cached_analysis(ttl=900)
def analyze_customer_behavior(
    from_date: str = None,
    to_date: str = None,
    company: str = None
) -> dict:
    """
    Analyze customer behavior and churn risk.

    Churn Risk Categories:
    - Low: Active in last 30 days
    - Medium: Active in last 60 days
    - High: Active in last 90 days
    - Critical: No activity in 90+ days
    """
```

### 3. Payment Pattern Analysis (AR Aging)

```python
@cached_analysis(ttl=900)
def analyze_payment_patterns(company: str = None) -> dict:
    """
    Analyze accounts receivable aging.

    Buckets:
    - Current (not yet due)
    - 1-30 days overdue
    - 31-60 days overdue
    - 61-90 days overdue
    - 90+ days overdue
    """
```

### 4. Supplier Performance

```python
@cached_analysis(ttl=900)
def analyze_supplier_performance(
    from_date: str = None,
    to_date: str = None
) -> dict:
    """
    Analyze supplier spend and activity.

    Returns:
    - Top suppliers by spend
    - Purchase frequency
    - Average order value
    """
```

### 5. Inventory Health

```python
@cached_analysis(ttl=900)
def analyze_inventory_health(warehouse: str = None) -> dict:
    """
    Analyze inventory status and alerts.

    Returns:
    - Stock levels vs reorder points
    - Low stock alerts
    - Overstock alerts
    - Stock turnover
    """
```

### Caching Decorator

All analysis tools use a caching decorator to avoid redundant database queries:

```python
def cached_analysis(ttl: int = 900):
    """
    Cache analysis results for specified TTL (default 15 minutes).

    Uses Redis if available, falls back to frappe.cache.
    """
```

---

## Security Architecture

Enterprise-grade security with multiple layers of protection.

### API Key Authentication

```python
# mcp/mcp/api/auth.py

def validate_api_key(api_key: str) -> str:
    """
    Validate API key and return linked user.

    Security measures:
    - Keys stored as SHA-256 hashes
    - Expiration date checking
    - IP whitelist validation
    - Failed attempt tracking
    """
```

### Rate Limiting

Redis-based sliding window rate limiting:

```python
# mcp/mcp/utils/security.py

def check_rate_limit(api_key_name: str) -> bool:
    """
    Check if request is within rate limit.

    Uses Redis atomic operations for distributed rate limiting.
    Sliding window algorithm with hourly buckets.
    """
```

### IP Filtering

CIDR notation support for IP whitelisting:

```python
def validate_ip_whitelist(client_ip: str, whitelist: str) -> bool:
    """
    Check if client IP is in whitelist.

    Supports:
    - Individual IPs: 192.168.1.100
    - CIDR notation: 192.168.1.0/24
    - Multiple entries: comma-separated
    """
```

### Audit Logging

Comprehensive logging of all API activity:

```python
# mcp/mcp/utils/audit.py

def log_request(
    api_key: str,
    method: str,
    tool_name: str,
    request_data: dict,
    response_status: str,
    execution_time: float,
    error_message: str = None
):
    """
    Log API request to MCP Audit Log.

    Captures:
    - API key used
    - Method/tool called
    - Request parameters
    - Response status
    - Execution time
    - Error details (if any)
    - Client IP
    - Timestamp
    """
```

---

## Performance & Caching

### Multi-Level Caching

```
┌─────────────────────────────────────────────────────────────────┐
│                     Caching Architecture                        │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ L1: Redis (TTL: 5 min)                                  │    │
│  │ - Rate limit counters                                   │    │
│  │ - Analysis results                                      │    │
│  │ - Query results                                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│                              ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ L2: Application Cache                                   │    │
│  │ - DocType metadata                                      │    │
│  │ - Permission mappings                                   │    │
│  │ - Tool registry                                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│                              ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ L3: MariaDB Query Cache                                 │    │
│  │ - SELECT query caching                                  │    │
│  │ - Index optimization                                    │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### Configuration Settings

```python
# MCP Server Settings - Performance Section

# Query caching
enable_query_cache = True
cache_backend = "redis"  # or "frappe"
default_cache_ttl = 300  # 5 minutes

# Query limits
query_timeout = 30  # seconds
max_query_limit = 1000
default_page_size = 20

# Analysis caching
analysis_cache_ttl = 900  # 15 minutes
```

### Query Optimization

```python
# Efficient queries with explicit field selection

def query_doctype(
    doctype: str,
    filters: dict = None,
    fields: list = None,
    limit: int = 20,
    order_by: str = None
) -> dict:
    """
    Query DocType with optimization.

    Best practices:
    - Always specify fields (no SELECT *)
    - Use indexed fields in filters
    - Limit result set
    - Use order_by for pagination
    """
```

---

## Installation & Configuration

### Prerequisites

- Frappe v15+
- ERPNext v15+
- Python 3.10+
- Redis (for rate limiting and caching)
- OpenAI API key (optional, for semantic search)

### Installation

```bash
# Get the app
cd frappe-bench
bench get-app mcp https://github.com/noreli-north/mcp

# Install on site
bench --site yoursite.local install-app mcp

# Run migrations
bench --site yoursite.local migrate

# Restart
bench restart
```

### Post-Installation Setup

1. **Configure Server Settings**

   ERPNext → MCP → MCP Server Settings

   Key settings:
   - Enable Server: ✓
   - Server URL: `http://localhost:8000`
   - Log Level: Standard
   - Default Rate Limit: 1000 requests/hour

2. **Create API Key**

   ERPNext → MCP → MCP API Key → New

   - Key Name: "Claude Integration"
   - Linked User: Administrator (or service user)
   - Rate Limit: 1000
   - IP Whitelist: (optional)

3. **Configure Data Sources**

   ERPNext → MCP → MCP Data Source

   Default sources created:
   - Customer
   - Supplier
   - Item
   - Sales Invoice
   - Purchase Invoice
   - User

4. **Configure Claude Code**

   Add to `~/.claude/claude_code_config.json`:

   ```json
   {
     "mcpServers": {
       "erpnext": {
         "command": "python",
         "args": ["-m", "mcp_erpnext"],
         "env": {
           "ERPNEXT_URL": "http://localhost:8000",
           "ERPNEXT_API_KEY": "your-api-key-here"
         }
       }
     }
   }
   ```

5. **Test Connection**

   ```
   curl -X POST http://localhost:8000/api/method/mcp.mcp.api.protocol.mcp_endpoint \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"jsonrpc":"2.0","method":"tools/list","id":1,"params":{}}'
   ```

### Knowledge Base Setup (Optional)

1. **Enable Semantic Search**

   MCP Server Settings → Semantic Search:
   - Enable Semantic Search: ✓
   - Embedding Model: text-embedding-3-small
   - Embedding Dimensions: 1536

2. **Configure AI Provider**

   AI Provider Settings (from ai_assistant app):
   - Provider: OpenAI
   - API Key: sk-...

3. **Index Documents**

   MCP → MCP Knowledge Document → New

   Options:
   - Upload PDF/DOCX/TXT files
   - Web crawl documentation sites
   - Auto-index help articles

---

## Real-World Usage Patterns

### Pattern 1: Autonomous Development

```
You are an autonomous developer. Create a project called "Payment
Reminder System" and break it into tasks. As you work:
- Update task status when you start and finish
- Log affected files
- Mark blockers if you encounter them
Start with the first task now.
```

### Pattern 2: Business Intelligence Query

```
Analyze my sales data for the last quarter. I want to know:
1. Top 10 products by revenue
2. Customer churn risk analysis
3. AR aging breakdown

Use the analysis tools and present the findings.
```

### Pattern 3: RAG-Powered Documentation

```
Search the knowledge base for information about creating custom
print formats in ERPNext. Summarize the process and provide
code examples.
```

### Pattern 4: Data Query and Report

```
Query all Sales Invoices from last month that are overdue.
Group by customer and show total outstanding amount.
Format as a table.
```

### Pattern 5: Status Report Generation

```
I have a standup in 5 minutes. Look at all Agent Projects
from yesterday and today. What was completed? What's in
progress? Any blockers? Format for quick presentation.
```

---

## What's Next

MCP Server for ERPNext continues to evolve:

### Planned Features

| Feature | Description | Status |
|---------|-------------|--------|
| **Streaming Responses** | Real-time tool execution updates | Planned |
| **Tool Chaining** | Automatic multi-tool workflows | Planned |
| **Event Webhooks** | Notify external systems on events | Planned |
| **GraphQL Support** | Alternative query interface | Exploring |
| **Multi-Tenant** | Per-company API key isolation | Planned |

### Community Contributions

I welcome contributions:
- New analysis tools
- Knowledge base extractors
- Custom tool templates
- Documentation improvements

---

## Summary

MCP Server for ERPNext transforms your ERP into an AI-accessible enterprise platform:

| Component | What It Provides |
|-----------|------------------|
| **Protocol Layer** | Standard JSON-RPC 2.0 MCP endpoint |
| **Task Management** | Full project tracking with Kanban, Gantt, execution logs |
| **Knowledge Base** | Hybrid search with keyword + semantic retrieval |
| **Analysis Tools** | Pre-built business intelligence queries |
| **Security** | API keys, rate limiting, IP filtering, audit logs |

All while maintaining **100% compliance** with Frappe and ERPNext standards.

The gap between "AI can help with tasks" and "AI is a fully integrated team member" is closing. MCP is the protocol. ERPNext is the system. Your AI assistant just got a whole lot smarter.
