🛰️ Agentic Logistics Incident Response
PepsiCo Supply Chain Incident Processing — ServiceNow x n8n Implementation
🏢 Project Story

Imagine PepsiCo trucks on the road delivering to major clients like Whole Foods. One breaks down. Normally that triggers hours of coordination (finance, logistics, customer notifications, and lots of manual updates)

The task was to build a system inside ServiceNow AI Agent Studio that automatically handles the chaos. It calculates how much the delay costs based on the customer’s contract, finds the best reroute option, and then coordinates the whole recovery effort through n8n — connecting ServiceNow, logistics, and retail systems all in one flow.
<img width="2452" height="1114" alt="image" src="https://github.com/user-attachments/assets/5c2976b4-97e7-45b7-98cf-5f33852056c9" />


🎯 Desired Goal
Create a system that makes intelligent decisions on its own, communicate with other systems, and handle errors gracefully.

🧠 How I Built It
1️⃣ Core Setup

Scoped app: PepsiCo Deliveries (x_snc_pepsico_de_0)

Custom tables:

Delivery Delay: Tracks route incidents, route options, and agent status updates.

Supply Agreement: Holds customer-specific delivery windows and penalty rates.

Added sample data for Whole Foods to simulate real financial penalties.

2️⃣ Agent 1 – Route Financial Analysis Agent
<img width="1848" height="829" alt="image" src="https://github.com/user-attachments/assets/a8dfa5a0-6908-41d1-81a0-ee9a127967df" />


This agent acts like PepsiCo’s financial analyst on autopilot.
It reads the delivery delay record, cross-references the Supply Agreement, and calculates how much each possible reroute would cost if a truck is late.
Then it writes the result back to the record and even creates an incident for visibility.

Tools Used:

Record Lookups

Script Tool (CalculateFinancialImpact)

JSON updates to store cost results

Automated status progression from pending → calculated

3️⃣ Agent 2 – Route Decision Agent

Once Agent 1 finishes its math, this agent takes over.
It picks the most cost-efficient route based on impact and time, updates the record to approved, and prepares to send the final decision out via n8n.

In a real environment, n8n would push that routing info to:

Logistics MCP (dispatch new route)

Retail MCP (notify customer)

ServiceNow MCP (update status to dispatched)

N8N Workflow
<img width="1122" height="531" alt="n8n workflow" src="https://github.com/user-attachments/assets/0e957a83-e285-48ce-9a0c-c7da8c7cfb0f" />


🗺️ Architecture Flow
Delivery Delay Record (pending)
        ↓
Agent 1: Financial Analysis (calculates penalties)
        ↓
Incident Record Created
        ↓
Agent 2: Route Decision (selects best route)
        ↓
Webhook → n8n Workflow
        ↓
→ Logistics MCP (executes reroute)
→ Retail MCP (notifies customer)
→ ServiceNow MCP (updates status)


(see Diagram.png in repo)

⚙️ If I had more time, I’d add

Parallel processing for multiple routes

Slack/Teams integration for real-time approval alerts


📊 Business Value

This system takes what woud used to be hours of manual back-and-forth and turns it into an intelligent, automated process.

For a company like PepsiCo, that means:

Faster response when deliveries fail

Fewer missed SLAs and financial penalties

Less manual coordination across systems

Data-driven decisions made in seconds

It’s a perfect example of how AI agents and workflow orchestration can transform operations at enterprise scale.

