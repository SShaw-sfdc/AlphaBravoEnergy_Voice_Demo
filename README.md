# Alpha Bravo Energy — Agentforce Voice Demo

A Salesforce demo accelerator showcasing an Agentforce Voice agent for a clean energy company. The agent handles inbound warranty and order status calls, autonomously resolves common inquiries, and escalates to a human service rep via Omni-Channel.

## What's Included

| Component | Type | Description |
|-----------|------|-------------|
| `Alpha_Bravo_Energy_Support_Agent_1` | Agent (AiAuthoringBundle) | Voice agent with 4 topics: Order Status, Warranty Policy, Warranty Claim Escalation, Off-Topic |
| `Alpha_Bravo_Energy_Support_Agent` | GenAiPlugin | Agent topics, actions, and instructions |
| `Get_Order_Status` | Flow + GenAiFunction | WISMO action — looks up order by number, returns status |
| `ABE_Voice_Call_NBA_Strategy` | Flow (RecommendationStrategy) | Next Best Actions shown to human rep after escalation |
| `ABE_Voice_Lookup_Contact` | Flow (Record-Triggered) | Stamps Contact__c on VoiceCall from caller phone number |
| `ABE_Voice_Escalate_to_Queue` | Flow | Omni-Channel escalation to human agent queue |
| `ABE_Voice_Route_to_Agent` | Flow | Screen pop and agent routing post-escalation |
| `SDO_Service_Voice_Call_Omni_Routing` | Flow | Omni-Channel routing flow for voice calls |
| `ABE_Agent_Order_Access` | Permission Set | Field/object access for the agent user |

## Prerequisites

- Salesforce org with **Service Cloud Voice** (Amazon Connect or partner telephony)
- **Agentforce / Einstein Generative AI** license
- **Omni-Channel** enabled
- **Salesforce Knowledge** enabled (for warranty KB articles)
- **Data Cloud** (optional, for Agent Analytics)

## Deploy

```bash
git clone https://github.com/YOUR_ORG/abe-voice-demo
cd abe-voice-demo
sf org login web --alias my-org
sf project deploy start --target-org my-org
```

## Post-Deploy Manual Steps

These components require manual configuration after deployment:

### 1. Assign the Bot User
- In the deployed org, open the **AlphaBravoEnergyVoiceAgent** bot
- Update the Bot User to a valid user in your org (Setup → Bots → Edit)

### 2. Activate the Agent
- Setup → **Agentforce Studio** → open **Alpha Bravo Energy Support Agent** → **Activate**

### 3. Associate a Voice Channel
- Setup → **Voice** → assign the agent to your Amazon Connect / telephony channel

### 4. Configure the Omni-Channel Queue
- Ensure a queue named **General Voice** (or update `ABE_Voice_Escalate_to_Queue` flow) exists and is Omni-Channel enabled with a routing configuration

### 5. Create Knowledge Articles
- Create and publish 4 Knowledge articles (see `kb-article-*.md` files in this repo for content):
  - General Warranty Policy
  - Battery System Warranty
  - Power Inverter Warranty
  - Wind Turbine Warranty
- Set **Visible in Customer Service Portal** on each article

### 6. Add Data Library (optional)
- Agentforce Studio → your agent → **Settings** → **Data Libraries** → add Einstein Data Library pointing to the Knowledge articles

### 7. Wire Next Best Actions component
- App Builder → **Voice Call - Default** page → add **Next Best Actions** component
- Set Strategy = **ABE Voice Call - Next Best Actions**
- Save & Activate

### 8. Enable Agent Analytics (optional)
- Setup → **Einstein Audit, Analytics, and Monitoring Setup** → enable **Agentforce Session Tracing**
- Setup → **Agent Analytics** → install **Service Agent Analytics**

## Knowledge Article Content

Markdown source for all 4 warranty KB articles is included in this repo:
- `kb-article-1-general-warranty-policy.md`
- `kb-article-2-power-inverter-warranty.md`
- `kb-article-3-battery-warranty.md`
- `kb-article-4-wind-turbine-warranty.md`

## Demo Script

1. Call the configured voice number
2. Agent greets and routes based on intent:
   - **"Where is my order?"** → agent asks for order number, returns status
   - **"What does my warranty cover?"** → agent answers from Knowledge
   - **"My battery is swelling"** → agent escalates to human rep
3. Human rep receives screen pop with Contact, VoiceCall record, NBA recommendations, and AI-generated call summary

## Contributing

This accelerator is maintained by the Salesforce Innovation team. Open an issue or PR for improvements.
