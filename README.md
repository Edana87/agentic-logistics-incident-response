# Agentic Logistics Incident Response  
## PepsiCo Supply Chain Incident Processing - ServiceNow Implementation  

---

## System Overview  

### **Company Context**  
Maintaining reliable supply chain operations is critical for PepsiCo to deliver products to major retail partners like Whole Foods. When delivery trucks experience breakdowns, rapid response and resolution are essential to minimize financial and operational impact.  

### **Business Problem**  
PepsiCo’s logistics operations required an intelligent system to:  
- Automatically calculate delay costs based on customer contracts.  
- Select optimal rerouting options considering both financial impact and delivery constraints.  
- Coordinate execution with external logistics providers and customer notifications without manual intervention.  

### **Project Goal**  
Develop an automated supply chain incident processing system leveraging:  
- **ServiceNow AI Agents**  
- **n8n Workflow Orchestration**  

This system aims to reduce manual effort and improve operational efficiency.  

---

## Prerequisites  

To work with this solution, you should have:  
- **ServiceNow AI Agent Studio** expertise.  
- Basic understanding of **n8n workflow design**.  
- Familiarity with **system integration concepts**.  
- Access credentials:  
  - ServiceNow MCP Bearer token.  
  - AWS Bedrock access keys for AI model integration.  
- **JavaScript knowledge** for script tool implementation.  

---

## Repository Setup  

**Repository Name:** `agentic-logistics-incident-response`  

### Directory Structure  
```
/agentic-logistics-incident-response  
├── README.md  
├── agentic-logistics-incident-response.xml  
├── n8n-workflow.json  
├── n8n-execution.log  
├── Diagram.png  
```

### Architecture Diagram  
*(Placeholder for architecture diagram image)*  

---

## System Component Flow  

### **High-Level Workflow**  
1. **Logistics Provider Breakdown Notification**  
2. **PepsiCo ServiceNow Financial Analysis Agent**  
3. **Route Decision Agent**  
4. **n8n Communication Coordination Agent**  

### **Components Overview**  
#### **Logistics Provider (Schneider):**  
Detects truck breakdowns and updates PepsiCo ServiceNow via MCP protocol.  

#### **ServiceNow AI Agents:**  
- **Agent 1 – Route Financial Analysis Agent:**  
  - Calculates delay costs and updates incidents.  
- **Agent 2 – Route Decision Agent:**  
  - Selects optimal routing and triggers external execution.  

#### **n8n Workflow:**  
Coordinates external logistics and customer systems, and updates statuses back to ServiceNow.  

*(Placeholder for system flow diagram image)*  

---

## Implementation Steps  

### **Step 1: Application Setup**  
- Scoped Application Name: `PepsiCo Deliveries`  
- Auto-generated Scope: `x_snc_pepsico_de_0`  

---

### **Step 2: Tables & Sample Records**  

#### **Delivery Delay Table**  
| Field             | Type        | Description                          |  
|-------------------|-------------|--------------------------------------|  
| `route_id`        | Integer (PK)| Unique route identifier              |  
| `truck_id`        | Integer     | Truck number                         |  
| `customer_id`     | Integer     | Default 1                            |  
| `problem_description` | String  | Breakdown details (4000 chars)       |  
| `proposed_routes` | String      | JSON array of route options          |  
| `calculated_impact` | String    | JSON of financial analysis           |  
| `chosen_option`   | String      | Selected route details               |  
| `status`          | String (16) | pending / calculated / approved / dispatched |  
| `assigned_to`     | Reference   | Execution context for AI agents      |  
| `incident_sys_id` | String      | Links to associated incident record  |  

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
```

#### **Supply Agreement Table**  
| Field               | Type        | Description                         |  
|---------------------|-------------|-------------------------------------|  
| `customer_id`       | Integer (PK)| Unique customer ID                  |  
| `customer_name`     | String      | Customer name (100 chars)           |  
| `deliver_window_hours` | Integer  | Contractual delivery window         |  
| `stockout_penalty_rate` | Integer | Cost per hour of delay ($)          |  

**Sample Record:**  
```json
{
  "customer_id": 1,
  "customer_name": "Whole Foods",
  "deliver_window_hours": 3,
  "stockout_penalty_rate": 250
}
```

---

### **Step 3: Use Case & Trigger Configuration**  
- **Trigger Table:** `Delivery Delay`  
- **Condition:** `Status = "pending"`  
- **Run As:** User referenced in `assigned_to` field.  
- **Testing:** Use AI Agent Studio to manually test triggers with sample `route_id`.  

---

### **Step 4: AI Agent Architecture**  

#### **Agent 1 – Route Financial Analysis Agent**  
- **Purpose:** Calculate financial impact of delivery disruptions.  
- **Tools:** Record lookup, incident creation, status updates.  
- **Responsibilities:**  
  - Query supply agreement and delivery delay data.  
  - Calculate delay costs per route.  
  - Create incident records and update delivery table.  
  - Progress workflow to the next agent.  

#### **Agent 2 – Route Decision Agent**  
- **Purpose:** Select optimal routes and trigger external execution.  
- **Tools:** Record lookup, route selection, webhook for n8n.  
- **Responsibilities:**  
  - Analyze route options considering cost and delivery constraints.  
  - Update incident priority.  
  - Trigger n8n workflow via webhook.  
  - Set workflow status to `approved`.  

---

### **Step 5: n8n Workflow & Logging**  

#### **Required Nodes:**  
- Webhook (receives routing decisions).  
- AI Agent (coordinates external calls).  
- AWS Bedrock Chat Model.  
- Logistics MCP Client.  
- Retail MCP Client.  
- ServiceNow MCP Client.  
- Webhook Response.  

#### **MCP Client Payload Examples:**  
**Logistics MCP Client:**  
```json
{
  "route_id": "751526",
  "truck_id": "1130",
  "chosen_option": {"option_id": "opt-2","route_number": 2,"distance_miles": 300,"eta_minutes": 103}
}
```  

**ServiceNow MCP Client:**  
```json
{
  "route_id": "751526",
  "status": "dispatched"
}
```

*(Placeholder for n8n workflow diagram image)*  

---

### **Step 6: Testing & Validation**  
- Validate Agent 1 calculates financial impacts on pending Delivery Delay records.  
- Confirm Agent 2 executes route selection after Agent 1.  
- Verify n8n workflow coordinates external systems.  
- Check workflow status progression: `pending → calculated → approved → dispatched`.  
- Validate incident creation, priority management, and status updates.  

---

## Optimization  

- **Webhook URL** configured as a variable for maintainability.  
- Efficient record lookups to reduce API calls.  

---

## Business Value  

- Reduces manual intervention in supply chain disruptions.  
- Minimizes financial penalties and optimizes delivery routes.  
- Provides end-to-end visibility and execution tracking.  
- Demonstrates scalable, AI-driven supply chain automation.  

---

## Deliverables  

- ServiceNow Update Set with all AI Agent Studio components.  
- n8n workflow (`n8n-workflow.json`) and execution log (`n8n-execution.log`).  
- Sample Delivery Delay and Supply Agreement records.  
- Architecture diagram (`Diagram.png`).  

*(Placeholder for summary image or diagram)*  
