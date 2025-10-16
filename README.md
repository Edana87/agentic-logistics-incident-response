# agentic-logistics-incident-response
# PepsiCo Supply Chain Incident Processing - ServiceNow Implementation

## System Overview
**Company Context:**  
As a ServiceNow AI Systems Developer at PepsiCo, maintaining reliable supply chain operations is critical for delivering products to major retail partners like Whole Foods. When delivery trucks experience breakdowns, rapid financial analysis and optimal rerouting decisions are essential to minimize customer impact and avoid contractual penalties.  

**Business Problem:**  
PepsiCo’s logistics operations required an intelligent system to:
- Automatically calculate delay costs based on customer contracts.
- Select optimal rerouting options considering both financial impact and delivery constraints.
- Coordinate execution with external logistics providers and customer notifications without manual intervention.  

**Project Goal:**  
Build an automated supply chain incident processing system leveraging ServiceNow AI agents and n8n workflow orchestration to reduce manual effort and improve operational efficiency.

---

## Prerequisites
- Understanding of **ServiceNow AI Agent Studio**
- Basic **n8n workflow design**
- Familiarity with **system integration concepts**
- Access credentials:
  - ServiceNow MCP Bearer token
  - AWS Bedrock access keys for AI model integration
- JavaScript knowledge for script tool implementation

---

## GitHub Repository Setup
**Repository Name:** `agentic-logistics-incident-response`  

**Directory Structure:**
/agentic-logistics-incident-response
├── README.md
├── agentic-logistics-incident-response.xml
├── n8n-workflow.json
├── n8n-execution.log
├── Diagram.png

yaml
Copy code

**Architecture Diagram:**  
Create using a tool like Draw.io and save as `Diagram.png`.

---

## System Component Flow
**High-Level Workflow:**
Logistics Provider Breakdown Notification
→ PepsiCo ServiceNow Financial Analysis Agent
→ Route Decision Agent
→ n8n Communication Coordination Agent

pgsql
Copy code

**Components:**
- **Logistics Provider (Schneider):** Detects truck breakdowns and updates PepsiCo ServiceNow via MCP protocol.
- **ServiceNow AI Agents:**  
  - **Agent 1 – Route Financial Analysis Agent:** Calculates delay costs and updates incidents.  
  - **Agent 2 – Route Decision Agent:** Selects optimal routing and triggers external execution.  
- **n8n Workflow:** Coordinates external logistics and customer systems, updates status back to ServiceNow.

---

## Implementation Steps

### Step 1: Application Setup
- Scoped Application Name: `PepsiCo Deliveries`
- Auto-generated scope: `x_snc_pepsico_de_0`

### Step 2: Tables & Sample Records

**Delivery Delay Table**
| Field | Type | Description |
|-------|------|-------------|
| route_id | Integer (PK) | Unique route identifier |
| truck_id | Integer | Truck number |
| customer_id | Integer | Default 1 |
| problem_description | String | Breakdown details (4000 chars) |
| proposed_routes | String | JSON array of route options |
| calculated_impact | String | JSON of financial analysis |
| chosen_option | String | Selected route details |
| status | String (16) | pending / calculated / approved / dispatched |
| assigned_to | Reference | Execution context for AI agents |
| incident_sys_id | String | Links to associated incident record |

**Sample Record:**
```json
{
  "route_id": 741379,
  "status": "pending",
  "problem_description": "Breakdown at I-95 MM 27 (engine)",
  "proposed_routes": [
    {"option_id": "opt-1","route_number": 16,"distance_miles": 92,"eta_minutes": 244},
    {"option_id": "opt-2","route_number": 20,"distance_miles": 145,"eta_minutes": 319},
    {"option_id": "opt-3","route_number": 8,"distance_miles": 78,"eta_minutes": 195}
  ],
  "truck_id": 39531,
  "customer_id": 1
}
Supply Agreement Table

Field	Type	Description
customer_id	Integer (PK)	Unique customer ID
customer_name	String	Name (100 chars)
deliver_window_hours	Integer	Contractual delivery window
stockout_penalty_rate	Integer	Cost per hour of delay ($)

Sample Record:

json
Copy code
{
  "customer_id": 1,
  "customer_name": "Whole Foods",
  "deliver_window_hours": 3,
  "stockout_penalty_rate": 250
}
Step 3: Use Case & Trigger Configuration
Trigger Table: Delivery Delay

Condition: Status = "pending"

Run As: User referenced in assigned_to field

Testing: Use AI Agent Studio to manually test triggers with sample route_id.

Step 4: AI Agent Architecture
Agent 1 – Route Financial Analysis Agent

Purpose: Calculate financial impact of delivery disruptions

Tools: Record lookup, incident creation, status updates

Responsibilities:

Query supply agreement and delivery delay data

Calculate delay costs per route

Create incident records and update delivery table

Progress workflow to next agent

Agent 2 – Route Decision Agent

Purpose: Select optimal routes and trigger external execution

Tools: Record lookup, route selection, webhook for n8n

Responsibilities:

Analyze route options considering cost and delivery constraints

Update incident priority

Trigger n8n workflow via webhook

Set workflow status to approved

Script Tools: Configure webhook URLs as variables for maintainability and logging.

Step 5: n8n Workflow & Logging
Required Nodes:

Webhook (receives routing decisions)

AI Agent (coordinates external calls)

AWS Bedrock Chat Model

Logistics MCP Client

Retail MCP Client

ServiceNow MCP Client

Webhook Response

MCP Client Payload Examples:

json
Copy code
// Logistics MCP Client
{
  "route_id": "751526",
  "truck_id": "1130",
  "chosen_option": {"option_id": "opt-2","route_number": 2,"distance_miles": 300,"eta_minutes": 103}
}

// ServiceNow MCP Client
{
  "route_id": "751526",
  "status": "dispatched"
}
Execution Logging: Consolidate all node input/output in n8n-execution.log.

Step 6: Testing & Validation
Validate Agent 1 calculates financial impacts on pending Delivery Delay records.

Confirm Agent 2 executes route selection after Agent 1.

Verify n8n workflow coordinates external systems.

Check workflow status progression: pending → calculated → approved → dispatched.

Validate incident creation, priority management, and status updates.

Architecture Diagram
Include Diagram.png illustrating:

Truck breakdown detection → ServiceNow AI agent workflow

Financial analysis → Route decision

n8n orchestration → External system coordination

Status progression & tracking

Optimization
Webhook URL configured as variable for maintainability

JSON payload structures standardized across all MCP clients

Efficient record lookups to reduce API calls

Future opportunities: caching, parallel execution, advanced error recovery, enhanced monitoring

Testing Results
Automatic triggers executed successfully

Financial impacts calculated accurately

Optimal routing selected according to constraints

External execution coordinated via n8n with logs consolidated

Business Value
Reduces manual intervention in supply chain disruptions

Minimizes financial penalties and optimizes delivery routes

Provides end-to-end visibility and execution tracking

Demonstrates scalable, AI-driven supply chain automation

Deliverables
ServiceNow Update Set with all AI Agent Studio components

n8n workflow (n8n-workflow.json) and execution log (n8n-execution.log)

Sample Delivery Delay and Supply Agreement records

Architecture diagram (Diagram.png)

Repository URL demonstrating full integratio
