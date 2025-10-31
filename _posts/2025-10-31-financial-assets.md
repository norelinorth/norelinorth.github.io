# Financial Asset Automation - Implementation Plan

**App Name:** `financial_asset_automation`
**Version:** 1.0.0
**Purpose:** Automate financial asset accounting and finance processes while maintaining 100% HGB, IFRS, and US-GAAP compliance

---

## Core Principles

### Standards Compliance (Non-Negotiable)
1. ✅ **100% Standard Frappe/ERPNext patterns** - No exceptions
2. ✅ **Zero custom fields** on core DocTypes - Use only built-in fields
3. ✅ **Zero core modifications** - All customizations via custom app
4. ✅ **No hardcoded fallback values** - All configuration from database
5. ✅ **Real database data only** - Fail gracefully with helpful errors

### Testing Requirements
- ✅ **Individual function testing** - Test each function in isolation
- ✅ **End-to-end testing** - Test complete workflows
- ✅ **Standards verification** - Verify Frappe/ERPNext alignment at every step

---

## 1. APP OVERVIEW

**Purpose:** Bring automation to financial asset-related accounting and finance processes

**Key Features:**
- Automated market data fetching (stock prices, bonds, etc.)
- Manual data entry capability
- AI-powered recommendations using ai assistant app
- Multi-standard support (Local GAAP, IFRS, US-GAAP)
- Dashboard and reporting
- Full audit trail

**Philosophy:**
- Assist accountants with automation and AI
- Never replace professional judgment
- Maintain legal compliance requirements
- Provide clear audit trails

---

## 2. CORE DOCTYPE STRUCTURE

### 2.1 Primary DocTypes

#### **Financial Asset** (Main DocType)
Standard Frappe submittable document for tracking securities.

**Key Fields:**
```python
{
    # Identification
    "naming_series": "FA-.YYYY.-",
    "asset_name": "Data",              # e.g., "Apple"
    "company": "Link: Company",        # Standard ERPNext field
    "status": "Select",                # Draft/Active/Sold/Impaired/Disposed

    # Security Identification
    "isin": "Data",                    # International Securities ID
    "ticker": "Data",                  # Stock ticker symbol
    "exchange": "Data",                # NYSE, etc.
    "security_type": "Select",         # Stock/Bond/Fund/ETF/Other
    "issuer": "Data",                  # Issuing entity

    # Acquisition Data
    "acquisition_date": "Date",
    "quantity": "Float",
    "cost_per_unit": "Currency",
    "acquisition_cost_total": "Currency",
    "transaction_costs": "Currency",
    "total_cost_basis": "Currency",    # Total acquisition cost

    # Current Valuation (Auto-fetched)
    "current_price": "Currency",
    "price_date": "Date",
    "price_source": "Data",            # "Yahoo Finance", "Manual Entry"
    "last_price_fetch": "Datetime",
    "market_value": "Currency",
    "unrealized_gain_loss": "Currency",

    # Accounting Standard Configuration
    "accounting_standard": "Select",   # Local GAAP/IFRS/US-GAAP

    # IFRS 9 Classification
    "ifrs9_classification": "Select",  # Amortized Cost/FVOCI/FVTPL
    "business_model": "Small Text",
    "sppi_test_result": "Check",

    # HGB Classification
    "hgb_category": "Select",          # Current Asset/Fixed Asset
    "intended_holding": "Select",      # Short-term/Long-term

    # US-GAAP Classification (ASC 320)
    "usgaap_category": "Select",       # Trading/AFS/HTM

    # Impairment Tracking
    "impairment_indicator": "Check",
    "impairment_reason": "Text",
    "cumulative_impairment": "Currency",
    "last_impairment_date": "Date",

    # Market Data Settings
    "auto_fetch_price": "Check",
    "price_fetch_frequency": "Select",
    "manual_price_override": "Check",

    # GL Integration
    "asset_account": "Link: Account",
    "unrealized_gain_account": "Link: Account",
    "cost_center": "Link: Cost Center",

    # Child Tables
    "price_history": "Table: FA Price History",
    "transactions": "Table: FA Transaction",
    "impairment_log": "Table: FA Impairment Log",
    "ai_recommendations": "Table: FA AI Recommendation"
}
```

#### **FA Price History** (Child Table)
```python
{
    "date": "Date",
    "price": "Currency",
    "source": "Data",
    "fetch_method": "Select",  # Auto/Manual
    "fetched_by": "Link: User"
}
```

#### **FA Transaction** (Child Table)
```python
{
    "transaction_date": "Date",
    "transaction_type": "Select",  # Buy/Sell/Split/Dividend
    "quantity": "Float",
    "price_per_unit": "Currency",
    "total_amount": "Currency",
    "reference_doc": "Dynamic Link"
}
```

#### **FA Impairment Log** (Child Table)
```python
{
    "impairment_date": "Date",
    "impairment_amount": "Currency",
    "reason": "Text",
    "accounting_standard": "Select",
    "approved_by": "Link: User",
    "journal_entry": "Link: Journal Entry"
}
```

#### **FA AI Recommendation** (Child Table)
```python
{
    "recommendation_date": "Datetime",
    "recommendation_type": "Select",  # Impairment/Reclassification/Alert
    "ai_analysis": "Long Text",
    "confidence_score": "Percent",
    "status": "Select",               # Pending/Approved/Rejected
    "reviewed_by": "Link: User",
    "review_notes": "Text"
}
```

---

### 2.2 Settings & Configuration DocTypes

#### **Financial Asset Settings** (Single DocType)
```python
{
    # General Settings
    "enable_financial_assets": "Check",
    "default_accounting_standard": "Select",

    # Market Data Settings
    "enable_auto_price_fetch": "Check",
    "default_data_provider": "Select",
    "price_fetch_schedule": "Select",
    "alpha_vantage_api_key": "Password",

    # Price Change Tolerance
    "price_change_alert_threshold": "Percent",
    "require_manual_review_above": "Percent",

    # AI Integration
    "enable_ai_recommendations": "Check",
    "ai_impairment_analysis": "Check",
    "ai_model_override": "Data",

    # Impairment Settings
    "hgb_permanent_decline_threshold": "Percent",
    "hgb_permanent_decline_duration": "Int",
    "ifrs_ecl_enable": "Check",

    # Account Mappings
    "default_accounts": "Table: FA Default Account"
}
```

#### **FA Default Account** (Child Table)
```python
{
    "company": "Link: Company",
    "asset_account": "Link: Account",
    "unrealized_gain_account_fvtpl": "Link: Account",
    "unrealized_gain_account_fvoci": "Link: Account",
    "realized_gain_account": "Link: Account",
    "impairment_expense_account": "Link: Account"
}
```

---

## 3. AI INTEGRATION ARCHITECTURE

### 3.1 Pydantic AI Agents (Intelligent Automation)

**Core Philosophy:** Use Pydantic AI agents to intelligently automate multi-step workflows while maintaining full Frappe/ERPNext standards compliance.

**Why Pydantic AI:**
- ✅ Type-safe structured outputs
- ✅ Tool-based architecture (matches our provider pattern)
- ✅ Built-in validation and error handling
- ✅ Easy integration with existing Python code
- ✅ Maintains audit trail

**Agent Architecture:**

```python
# financial_asset_automation/financial_asset_automation/agents/base_agent.py

from pydantic import BaseModel, Field
from pydantic_ai import Agent, RunContext
from typing import Optional, Dict, Any
import frappe
from frappe import _

class AgentResult(BaseModel):
    """Standard result format for all agents"""
    success: bool
    data: Optional[Dict[str, Any]] = None
    recommendation: Optional[str] = None
    confidence: float = Field(ge=0.0, le=1.0)
    reasoning: str
    requires_approval: bool = True

class BaseFinancialAgent:
    """Base class for all financial asset agents"""

    def __init__(self):
        # Get AI provider config from ai_assistant app
        self.ai_config = frappe.call("ai_assistant.ai_provider_api.get_ai_config")

    def log_agent_action(self, agent_name: str, action: str, result: AgentResult):
        """Standard Frappe audit trail pattern"""
        frappe.log_error(
            message=frappe.as_json({
                "agent": agent_name,
                "action": action,
                "result": result.dict(),
                "user": frappe.session.user,
                "timestamp": frappe.utils.now()
            }),
            title=f"Agent Action: {agent_name}"
        )
```

### 3.2 Specialized AI Agents

#### **Agent 1: Impairment Analysis Agent**

```python
# financial_asset_automation/financial_asset_automation/agents/impairment_agent.py

from pydantic import BaseModel
from pydantic_ai import Agent
import frappe
from frappe.utils import flt

class ImpairmentAnalysisInput(BaseModel):
    asset_name: str
    isin: str
    acquisition_cost: float
    current_market_value: float
    accounting_standard: str
    price_history: list

class ImpairmentAnalysisResult(BaseModel):
    requires_impairment: bool
    impairment_amount: float
    reasoning: str
    confidence_score: float
    suggested_journal_entry: dict

class ImpairmentAnalysisAgent(BaseFinancialAgent):
    """
    Intelligent agent for analyzing impairment indicators
    Uses Pydantic AI for structured reasoning
    """

    def __init__(self):
        super().__init__()
        self.agent = Agent(
            'openai:gpt-4o',
            result_type=ImpairmentAnalysisResult,
            system_prompt=self._get_system_prompt()
        )

    def _get_system_prompt(self) -> str:
        return """You are a financial accounting expert specializing in asset impairment analysis.

        Your role:
        1. Analyze financial assets for impairment indicators
        2. Apply accounting standards (HGB, IFRS 9, US-GAAP) correctly
        3. Provide clear reasoning for your conclusions
        4. Suggest appropriate accounting entries

        Important:
        - Be conservative in your analysis (prudence principle)
        - Consider both quantitative and qualitative factors
        - Reference specific accounting standard requirements
        - Always provide confidence scores
        """

    async def analyze_impairment(self, asset_doc) -> ImpairmentAnalysisResult:
        """
        Multi-step intelligent impairment analysis
        Standard Frappe pattern: permission check, then process
        """
        # Permission check
        if not frappe.has_permission("Financial Asset", "write"):
            frappe.throw(_("No permission to perform impairment analysis"))

        # Prepare structured input
        input_data = ImpairmentAnalysisInput(
            asset_name=asset_doc.asset_name,
            isin=asset_doc.isin,
            acquisition_cost=flt(asset_doc.total_cost_basis),
            current_market_value=flt(asset_doc.market_value),
            accounting_standard=asset_doc.accounting_standard,
            price_history=[
                {"date": str(h.date), "price": flt(h.price)}
                for h in asset_doc.price_history[-30:]
            ]
        )

        # Run agent analysis
        result = await self.agent.run(
            f"Analyze this asset for impairment under {asset_doc.accounting_standard}",
            message_history=[{"role": "user", "content": input_data.model_dump_json()}]
        )

        # Log action (standard Frappe audit trail)
        self.log_agent_action(
            agent_name="ImpairmentAnalysisAgent",
            action="analyze_impairment",
            result=AgentResult(
                success=True,
                data=result.data.dict(),
                recommendation=result.data.reasoning,
                confidence=result.data.confidence_score,
                requires_approval=True
            )
        )

        return result.data

# Whitelisted API method
@frappe.whitelist()
def run_impairment_analysis(asset_name):
    """
    Standard Frappe whitelisted method
    Runs async agent in sync context
    """
    import asyncio

    if not frappe.has_permission("Financial Asset", "write"):
        frappe.throw(_("No permission"))

    asset = frappe.get_doc("Financial Asset", asset_name)
    agent = ImpairmentAnalysisAgent()

    # Run async agent
    result = asyncio.run(agent.analyze_impairment(asset))

    # Store recommendation in asset
    asset.append("ai_recommendations", {
        "recommendation_date": frappe.utils.now(),
        "recommendation_type": "Impairment",
        "ai_analysis": result.reasoning,
        "confidence_score": result.confidence_score * 100,
        "status": "Pending"
    })
    asset.save()

    return result.dict()
```

#### **Agent 2: Classification Agent**

```python
# financial_asset_automation/financial_asset_automation/agents/classification_agent.py

class IFRS9ClassificationResult(BaseModel):
    classification: str  # "Amortized Cost" | "FVOCI" | "FVTPL"
    reasoning: str
    sppi_test_passed: bool
    business_model_assessment: str
    confidence_score: float

class ClassificationAgent(BaseFinancialAgent):
    """
    Intelligent agent for IFRS 9 / HGB / US-GAAP classification
    Uses multi-step reasoning to determine correct classification
    """

    async def suggest_ifrs9_classification(self, asset_doc) -> IFRS9ClassificationResult:
        """
        Multi-step classification analysis:
        1. Assess business model (hold to collect vs. trading)
        2. Perform SPPI test
        3. Determine classification
        4. Provide reasoning
        """
        # Implementation using Pydantic AI tools
        pass
```

#### **Agent 3: Price Validation Agent**

```python
# financial_asset_automation/financial_asset_automation/agents/price_validation_agent.py

class PriceValidationResult(BaseModel):
    is_valid: bool
    anomaly_detected: bool
    anomaly_type: Optional[str]  # "sudden_spike" | "correlation_break" | "trading_halt"
    recommended_action: str  # "accept" | "manual_review" | "reject"
    reasoning: str

class PriceValidationAgent(BaseFinancialAgent):
    """
    Intelligent validation of fetched market prices
    Detects anomalies and recommends actions
    """

    async def validate_price_update(
        self,
        old_price: float,
        new_price: float,
        price_history: list,
        market_indices: dict
    ) -> PriceValidationResult:
        """
        Multi-factor price validation:
        1. Check for sudden spikes (>20% change)
        2. Compare with market indices correlation
        3. Check for trading halts/circuit breakers
        4. Analyze volume patterns
        5. Recommend action
        """
        # Agent uses tools to check external data
        pass
```

#### **Agent 4: GL Entry Generation Agent**

```python
# financial_asset_automation/financial_asset_automation/agents/gl_agent.py

class GLEntryResult(BaseModel):
    journal_entry: dict
    accounting_entries: list
    narration: str
    requires_approval: bool
    compliance_notes: str

class GLGenerationAgent(BaseFinancialAgent):
    """
    Intelligent GL entry generation based on accounting standard
    Ensures correct debits/credits per HGB/IFRS/US-GAAP
    """

    async def generate_gl_entries(
        self,
        transaction_type: str,  # "acquisition" | "impairment" | "fair_value_adjustment"
        asset_doc,
        accounting_standard: str
    ) -> GLEntryResult:
        """
        Generate correct GL entries based on:
        1. Accounting standard rules
        2. Asset classification
        3. Company-specific account mappings
        4. Transaction type
        """
        # Agent ensures correct accounting treatment
        pass
```

### 3.3 Agent Orchestration

**Workflow Orchestrator Agent:**

```python
# financial_asset_automation/financial_asset_automation/agents/orchestrator.py

class WorkflowOrchestrator(BaseFinancialAgent):
    """
    Master agent that coordinates other specialized agents
    Manages complex multi-step workflows
    """

    async def process_new_asset(self, asset_doc):
        """
        Orchestrate full asset onboarding:
        1. Classification agent → Suggest classification
        2. Price validation agent → Validate initial price
        3. GL generation agent → Create acquisition entries
        4. Return summary for user approval
        """
        results = {}

        # Step 1: Classification
        classification_agent = ClassificationAgent()
        results['classification'] = await classification_agent.suggest_ifrs9_classification(asset_doc)

        # Step 2: Price validation
        price_agent = PriceValidationAgent()
        results['price_validation'] = await price_agent.validate_price_update(
            old_price=None,
            new_price=asset_doc.current_price,
            price_history=[],
            market_indices={}
        )

        # Step 3: GL entries
        if results['price_validation'].is_valid:
            gl_agent = GLGenerationAgent()
            results['gl_entries'] = await gl_agent.generate_gl_entries(
                transaction_type="acquisition",
                asset_doc=asset_doc,
                accounting_standard=asset_doc.accounting_standard
            )

        return results

    async def process_price_update(self, asset_doc, new_price):
        """
        Orchestrate price update workflow:
        1. Validate price
        2. Check impairment indicators
        3. Generate fair value adjustment entries (if needed)
        4. Return recommendations
        """
        pass
```

### 3.4 Integration with ai_assistant App

**Dual Integration Pattern:**

```python
# Option 1: Use ai_assistant for simple prompts
ai_response = frappe.call(
    "ai_assistant.ai_provider_api.call_ai",
    prompt=simple_prompt
)

# Option 2: Use Pydantic AI agents for complex workflows
agent = ImpairmentAnalysisAgent()
result = await agent.analyze_impairment(asset_doc)
```

**Benefits:**
- ✅ Simple queries → ai_assistant (quick, straightforward)
- ✅ Complex workflows → Pydantic AI agents (structured, multi-step)
- ✅ Both share same AI provider configuration
- ✅ Complete audit trail maintained

### 3.5 AI Use Cases

1. **Impairment Analysis** (Pydantic AI Agent)
   - Multi-step analysis with structured reasoning
   - Standard-specific logic (HGB vs. IFRS vs. US-GAAP)
   - Confidence scoring
   - Suggested journal entries

2. **Classification Assistance** (Pydantic AI Agent)
   - IFRS 9: Business model + SPPI test
   - HGB: Current vs. Fixed asset determination
   - US-GAAP: Trading/AFS/HTM categorization

3. **Price Validation** (Pydantic AI Agent)
   - Anomaly detection
   - Market correlation analysis
   - Recommend accept/review/reject

4. **GL Entry Generation** (Pydantic AI Agent)
   - Standard-compliant accounting entries
   - Automatic account mapping
   - Narration generation

5. **Simple Q&A** (ai_assistant direct calls)
   - "What's the carrying amount under HGB?"
   - "Is this asset impaired?"
   - User queries via chatbot

---

## 4. MARKET DATA INTEGRATION

### 4.1 Provider Architecture

**Pluggable provider system:**

```python
class MarketDataProvider(ABC):
    @abstractmethod
    def fetch_price(self, isin=None, ticker=None, exchange=None, date=None):
        pass

    @abstractmethod
    def validate_credentials(self):
        pass
```

**Providers:**
1. **Yahoo Finance** (free, no API key)
2. **Alpha Vantage** (free tier: 500 calls/day)
3. **ECB** (European Central Bank - official EU data)

### 4.2 Scheduled Price Updates

```python
# hooks.py
scheduler_events = {
    "daily": [
        "financial_asset_automation.schedulers.price_updater.daily_price_update"
    ]
}
```

**Features:**
- Automated daily updates (configurable)
- Price validation (alert on large movements)
- Manual override capability
- Complete audit trail

---

## 5. ACCOUNTING STANDARDS IMPLEMENTATION

### 5.1 HGB (German Commercial Code)

**Principles:**
- Historical cost principle
- Lower of cost or market
- Prudence principle
- Permanent impairment test

**Implementation:**
```python
def get_carrying_amount_hgb(asset_doc):
    cost = flt(asset_doc.total_cost_basis)
    market = flt(asset_doc.market_value)
    cumulative_impairment = flt(asset_doc.cumulative_impairment)

    return min(cost, market) - cumulative_impairment
```

### 5.2 IFRS 9 (International Financial Reporting Standards)

**Classifications:**
1. Amortized Cost
2. Fair Value Through OCI (FVOCI)
3. Fair Value Through P&L (FVTPL)

**Impairment:** Expected Credit Loss (ECL) model
- Stage 1: 12-month ECL
- Stage 2: Lifetime ECL
- Stage 3: Credit-impaired

### 5.3 US-GAAP (ASC 320)

**Categories:**
1. Trading Securities
2. Available-for-Sale (AFS)
3. Held-to-Maturity (HTM)

**Impairment:** Other-Than-Temporary Impairment (OTTI)

---

## 6. DASHBOARD & REPORTING

### 6.1 Workspace

**Financial Assets Workspace:**
- Dashboard charts (asset allocation, G/L trends)
- Quick shortcuts (new asset, reports, settings)
- KPIs (total portfolio value, unrealized G/L, alerts)

### 6.2 Standard Reports

1. **Financial Asset Portfolio**
   - Current holdings
   - Market values
   - Unrealized gains/losses
   - Carrying amounts by standard

2. **Impairment Analysis**
   - Assets with indicators
   - AI recommendations pending
   - Historical impairments

3. **Price Movement Alert**
   - Significant price changes
   - Requires manual review

4. **Accounting Standard Comparison**
   - Side-by-side view of carrying amounts under different standards

---

## 7. IMPLEMENTATION ROADMAP

### Phase 1: Foundation (Weeks 1-2)
**Goal:** Basic app structure with core DocTypes

**Tasks:**
1. Create app skeleton using bench
2. Create Financial Asset Settings (Single)
3. Create Financial Asset (main DocType)
4. Create child tables (Price History, Transaction)
5. Implement basic validation
6. Test CRUD operations

**Deliverables:**
- ✅ App installed and functional
- ✅ Can create and view Financial Assets
- ✅ Basic calculations working (market value, unrealized G/L)
- ✅ No custom fields on core ERPNext DocTypes

**Testing:**
- Create 5 test assets manually
- Verify all calculations
- Test permissions
- End-to-end: Create → Save → Submit workflow

---

### Phase 2: Market Data Integration (Weeks 3-4)
**Goal:** Automated price fetching

**Tasks:**
1. Create base provider class
2. Implement Yahoo Finance integration
3. Implement Alpha Vantage integration (optional)
4. Create scheduled task for daily updates
5. Add price validation logic
6. Implement manual override

**Deliverables:**
- ✅ Automated price updates working
- ✅ Price history logged with audit trail
- ✅ Alerts for unusual movements
- ✅ Manual override functional

**Testing:**
- Fetch prices for 10 test assets
- Verify scheduler runs correctly
- Test manual price entry
- End-to-end: Create asset → Auto-fetch → Review history

---

### Phase 3: AI Integration (Weeks 5-6)
**Goal:** AI-powered recommendations

**Tasks:**
1. Create AI helper module
2. Implement impairment analysis function
3. Implement classification suggestion
4. Add FA AI Recommendation child table
5. Create review workflow
6. Add automated triggers

**Deliverables:**
- ✅ AI analysis functional
- ✅ Recommendations logged and reviewable
- ✅ Integration with ai_assistant app verified
- ✅ Approve/reject workflow working

**Testing:**
- Trigger AI analysis for 5 assets
- Review recommendations
- Test approval workflow
- End-to-end: Price drop → AI analysis → Review → Approve/Reject

---

### Phase 4: Accounting Standards (Weeks 7-9)
**Goal:** HGB, IFRS, US-GAAP compliance

**Tasks:**
1. Create accounting_standards module structure
2. Implement HGB logic (lower of cost or market)
3. Implement IFRS 9 logic (3 classifications)
4. Implement US-GAAP logic (ASC 320)
5. Add carrying amount calculation
6. Implement impairment logic per standard
7. Create GL integration (Journal Entries)

**Deliverables:**
- ✅ All three standards implemented
- ✅ Correct carrying amounts calculated
- ✅ GL integration working
- ✅ Impairment processing functional per standard

**Testing:**
- Test same asset under all 3 standards
- Verify carrying amounts differ correctly
- Test impairment workflows
- End-to-end: Create asset → Calculate carrying amount → Post GL entries

---

### Phase 5: Reporting & Dashboard (Weeks 10-11)
**Goal:** User-friendly visualization

**Tasks:**
1. Create Financial Assets workspace
2. Add dashboard charts
3. Create Financial Asset Portfolio report
4. Create Impairment Analysis report
5. Create Price Movement Alert report
6. Add custom dashboards

**Deliverables:**
- ✅ Workspace functional with charts
- ✅ All reports working
- ✅ Dashboards displaying real-time data

**Testing:**
- Generate all reports with test data
- Verify chart calculations
- User acceptance testing
- End-to-end: Create assets → View dashboard → Generate reports

---

### Phase 6: Polish & Documentation (Week 12)
**Goal:** Production-ready

**Tasks:**
1. Code review for standards compliance
2. Write README.md
3. Write INSTALLATION.md
4. Write user guide
5. Create sample data fixtures
6. End-to-end testing
7. Performance testing

**Deliverables:**
- ✅ App ready for deployment
- ✅ Documentation complete
- ✅ All tests passing
- ✅ Standards compliance verified

**Testing:**
- Full end-to-end testing
- Multi-company testing
- Multi-standard testing
- Performance testing (100+ assets)

---

## 8. STANDARDS COMPLIANCE CHECKLIST

Before each phase completion, verify:

- [ ] **No custom fields on core DocTypes**
- [ ] **No hardcoded values** - All config from database
- [ ] **No core modifications**
- [ ] **Clean imports** - No sys.path manipulation
- [ ] **Permission checks** - All @frappe.whitelist() protected
- [ ] **Error logging** - Uses frappe.log_error()
- [ ] **Translatable strings** - All messages use _()
- [ ] **SQL safety** - All queries parameterized
- [ ] **Type conversion** - Uses flt(), cint(), cstr()
- [ ] **No redundant fallbacks** - No flt(x or 0)
- [ ] **Standard Frappe ORM** - Avoid raw SQL
- [ ] **Proper fixtures** - Workspace, settings
- [ ] **Hooks registered** - schedulers, doc_events
- [ ] **GL entries** - Standard ERPNext pattern

---

## 9. TESTING STRATEGY

### Individual Function Testing
Test each function in isolation:
```python
# Example: Test carrying amount calculation
def test_get_carrying_amount_hgb():
    asset = frappe.get_doc({
        "doctype": "Financial Asset",
        "total_cost_basis": 10000,
        "market_value": 8000,
        "cumulative_impairment": 0
    })

    result = get_carrying_amount_hgb(asset)
    assert result == 8000  # Lower of cost or market
```

### End-to-End Testing
Test complete workflows:
```python
# Example: Full asset lifecycle
def test_asset_lifecycle_e2e():
    # 1. Create asset
    asset = create_test_asset()

    # 2. Fetch price
    fetch_market_price(asset)

    # 3. Calculate carrying amount
    carrying = asset.get_carrying_amount()

    # 4. Check impairment
    if asset.requires_impairment():
        create_impairment_entry(asset)

    # 5. Verify GL entries
    gl_entries = get_gl_entries_for_asset(asset)
    assert len(gl_entries) > 0
```

### Standards Verification
Run compliance checks after each change:
```bash
# Check for hardcoded values
grep -r "0\.01\|fallback" apps/financial_asset_automation/

# Check for custom fields
frappe.get_meta("Sales Invoice").get_custom_fields()

# Check for core modifications
git diff apps/erpnext/
```

---

## 10. SUCCESS CRITERIA

**App is successful if:**

1. ✅ **Marketplace-ready** - Could be submitted without modifications
2. ✅ **Standards-compliant** - 100% Frappe/ERPNext patterns
3. ✅ **Accounting-compliant** - HGB, IFRS, US-GAAP correct
4. ✅ **AI-enhanced** - Meaningful recommendations
5. ✅ **User-friendly** - Accountants can use without technical knowledge
6. ✅ **Production-tested** - Works with real portfolios (100+ assets)
7. ✅ **Well-documented** - Clear guides

---

## 11. NEXT STEPS

**Start Phase 1:**
1. Create app skeleton
2. Install on erpnext.local
3. Create first DocType (Financial Asset Settings)
4. Test basic functionality

---

## Appendix A: Standard Frappe Patterns Reference

### 1. DocType Validation
```python
def validate(self):
    # Standard Frappe pattern
    if not self.acquisition_date:
        frappe.throw(_("Acquisition Date is required"))

    # Recalculate values
    self.market_value = flt(self.quantity) * flt(self.current_price)
```

### 2. Permission Checks
```python
@frappe.whitelist()
def fetch_price(asset_name):
    # Standard Frappe pattern
    if not frappe.has_permission("Financial Asset", "write"):
        frappe.throw(_("No permission to update prices"))
```

### 3. Error Logging
```python
try:
    price_data = fetch_from_yahoo(ticker)
except Exception as e:
    # Standard Frappe pattern
    frappe.log_error(
        message=f"Failed to fetch price: {str(e)}",
        title="Market Data Error"
    )
    frappe.throw(_("Unable to fetch price. Please try again."))
```

### 4. Database Queries
```python
# Standard Frappe ORM pattern
assets = frappe.get_all(
    "Financial Asset",
    filters={"company": company, "status": "Active"},
    fields=["name", "asset_name", "market_value"]
)
```

---

**Document Version:** 1.0
**Last Updated:** 2025-10-17
**Status:** Ready for Phase 1 Implementation
