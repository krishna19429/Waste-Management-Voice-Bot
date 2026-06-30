# 13 — Testing 

 

## Test Strategy 

 

| Layer | Tests | Tool | 
|-------|-------|------| 
| NLU / Intent | Intent accuracy on varied phrasing | CX Test Cases | 
| Flow / Page | Route transitions, session params | CX Simulator | 
| Webhook / API | Cloud Function response validation | Simulator + Cloud Logs | 
| Happy Path | End-to-end complete call | Phone Gateway | 
| Edge Cases | Auth failure, timeout, no-match | CX Simulator | 
| Voice / IVR | Real call — ASR, TTS quality | Phone call | 

 

--- 

 

## Authentication Test Cases 

 

| TC ID | Scenario | Input | Expected | Pass/Fail | 
|-------|----------|-------|----------|-----------| 
| TC-A01 | Valid phone + valid address | 9876543210 / Baner Pune | is_authenticated=true, customer_id=C001 | | 
| TC-A02 | Valid phone + wrong address | 9876543210 / Wakad Pune | auth_fail, retry prompt | | 
| TC-A03 | Phone not in DB | 1111111111 / any | phone_not_found, retry | | 
| TC-A04 | Three failures | wrong data x3 | Escalation Agent triggered | | 
| TC-A05 | No input during phone | silence 5s | no-input handler fires | | 
| TC-A06 | Unrelated speech | what is the weather | no-match handler fires | | 
| TC-A07 | Address with extra words | it is in Baner area Pune | flexible match succeeds | | 

 

--- 

 

## Service Flow Test Cases 

 

| TC ID | Flow | Input | Expected | API Called | Pass/Fail | 
|-------|------|-------|----------|------------|-----------| 
| TC-SS01 | Status | check my service status (C001) | Scheduled, June 20th | checkServiceStatus() | | 
| TC-SS02 | Status | check my service status (C003) | Delayed status | checkServiceStatus() | | 
| TC-MP01 | Missed Pickup | garbage not picked up (C001) | Ticket MP-C001-20260620 | reportMissedPickup() | | 
| TC-RS01 | Reschedule | reschedule to next Monday (C001) | Date confirmed, API called after yes | reschedulePickup() | | 
| TC-RS02 | Reschedule | caller says no to confirmation | Re-ask for date, API NOT called | None | | 
| TC-RD01 | Route Delay | why is truck late (C003) | 2 hours, traffic | getRouteDelayInfo() | | 
| TC-CB01 | Blockage | container is blocked (C004) | Priority ticket created | checkContainerStatus() | | 
| TC-CP01 | Complaint | raise a complaint (C001) | Ticket SR-C001-20260620, 24-hr | createServiceRequest() | | 

 

--- 

 

## NLU Intent Accuracy Tests 

 

| TC ID | Phrase | Expected Intent | Pass/Fail | 
|-------|--------|----------------|-----------| 
| NLU-01 | Pickup was skipped this morning | service.missed_pickup | | 
| NLU-02 | I need to know when my collection is | service.check_status | | 
| NLU-03 | Move my collection to Thursday | service.reschedule | | 
| NLU-04 | Garbage truck not here yet | service.route_delay | | 
| NLU-05 | Car blocking my dumpster | service.blockage | | 
| NLU-06 | I am unhappy and want to complain | service.complaint | | 
| NLU-07 | What is two plus two | No match — fallback | | 

 

--- 

 

## UAT Checklist 

 

- [ ] All 7 intents recognize 5+ varied phrasing variations 

- [ ] Authentication succeeds for all 4 mock customers (C001-C004) 

- [ ] All 7 service flows complete end-to-end in CX simulator 

- [ ] All 9 webhooks return correct JSON in Cloud Logs 

- [ ] Phone Gateway assigned and bot answers incoming call 

- [ ] No-input handler fires after 5 seconds silence on all pages 

- [ ] No-match handler fires for gibberish on all pages 

- [ ] Escalation triggers after 3 auth failures 

- [ ] Re-routing works mid-conversation without re-authentication
