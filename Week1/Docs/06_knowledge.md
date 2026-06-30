### Overview

The Knowledge Base contains business policies, FAQs, pickup schedules, holiday rules, and escalation policies. This information is available to all agents as background context. It informs responses without requiring an API call for every question.

### Business Policies
#### Authentication Policy
- Authentication is required exactly once per call session
- Two-factor verification: phone number AND service address both required
- Three-strike policy: after 3 failed authentication attempts, escalate to human agent
- Session authentication persists for the entire call — no re-authentication for re-routing
- Verified customer information (customer_id, name, address) is reused throughout the call

#### Service Coverage Policy
- This bot handles ONLY waste management service queries
- Billing, payments, account creation, contract changes are outside bot scope
- Out-of-scope requests: acknowledge, explain scope, offer human agent escalation

#### Ticket Resolution Policy
- Missed pickup: committed resolution next business day
- Container blockage: field team follow-up within 2 hours
- Formal complaint: senior representative callback within 24 hours
- Reschedule: confirmed immediately on successful API call
- All tickets receive a unique reference ID — always read this back to the caller

### FAQs — Common Questions

|Customer Question| Bot Response Policy|
|-----------------|--------------------|
|What are your service hours?| Waste management service operates Monday to Saturday 7 AM to 6 PM. Bot is available 24/7.|
|Why was my pickup missed?| Bot cannot diagnose root cause. Log a missed pickup report for resolution.|
|Can I cancel my service?| Outside bot scope. Transfer to human agent for account-level changes.|
|Is there a charge for rescheduling?| No charge for first reschedule per month. Bot cannot process payments.|
|When will my complaint be resolved?| Senior representative will call within 24 hours. Provide ticket ID if caller asks for status.|
|What do I do if the truck comes but doesn't take my waste?| This is a missed pickup — route to Missed Pickup Agent.|

### Holiday and Special Scheduling Policy
- No collections on national public holidays — service pushed to next available day
- If rescheduled date falls on a holiday, bot should notify caller and suggest next available weekday
- Monsoon season (June to September): route delays are common — Route Delay Agent must communicate proactively

### Escalation Policy
- Always escalate with full context: customer_id, intents attempted, tickets created, reason for escalation
- Never escalate without acknowledging the transfer to the caller
- Estimated wait time must be provided if available
- After escalation, bot ends session — do not re-engage the same caller
