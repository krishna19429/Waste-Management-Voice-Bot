### Overview

There are 9 tools registered in CX Agent Studio. Each tool is a named function that the LLM can call when needed. Tools are defined in CX Agent Studio under each Sub-Agent's configuration and map to specific Python functions deployed in the Cloud Function webhook.

When CX invokes a tool, it sends a POST request to the Cloud Function URL with a tag identifying which API to call, along with the current session parameters as the request payload.


|Tool Name| Sub-Agent| API Function| Type|
|---------|----------|--------------|----|
|AddressValidationTool| Authentication Agent| validateAddress(phone, address)| Write — sets session auth state|
|ServiceStatusTool| Service Status Agent| checkServiceStatus(customer_id)| Read-only|
|MissedPickupTool| Missed Pickup Agent| reportMissedPickup(customer_id, date) |Write — creates ticket|
|RescheduleTool| Reschedule Agent| reschedulePickup(customer_id, new_date)| Write — updates schedule|
|RouteDelayTool| Route Delay Agent| getRouteDelayInfo(route_id) |Read-only|
|ContainerCheckTool |Container Blockage Agent| checkContainerStatus(customer_id)| Write — creates priority ticket|
|ServiceRequestTool| Complaint Agent| createServiceRequest(customer_id, desc)| Write — creates complaint|
|CustomerInfoTool| Authentication Agent| getCustomerProfile(customer_id)| Read-only|
|NextPickupTool| Service Status Agent| getNextPickupDate(customer_id)| Read-only|


### Tool Detail: AddressValidationTool

### Purpose
Authenticates the caller by verifying that the provided phone number and service address match an active account in the customer database.

### Input Parameters
- phone_number: string — 10-digit registered phone number
- service_address: string — service address as spoken by caller

### Expected Output
// Success 
{"verified": true, "serviceable": true, "customerId": "C001", "name": "Ankit Sharma"} 

// Failure — address mismatch 
{"verified": false, "reason": "address_mismatch"} 

// Failure — phone not found 
{"verified": false, "reason": "phone_not_found"}

### Error Handling
- On verified=false: do NOT escalate immediately — allow 3 total attempts
- On API timeout: inform caller of technical issue, offer callback

### Tool Detail: MissedPickupTool

#### Purpose
Logs a missed pickup complaint against the customer account and generates a service ticket with a committed resolution date.

### Input Parameters
- customer_id: string — from session param
- missed_date: date — confirmed pickup date that was missed

### Expected Output

{"status": "Request Logged", "ticketId": "MP-C001-20260620", "resolutionDate": "2026-06-21"}

### Error Handling
- Retry once on timeout
- On second failure: escalate with ticket attempt logged

### Tool Detail: RescheduleTool

#### Purpose
Updates the pickup schedule for a customer account to a new requested date. Only called after explicit verbal confirmation from caller.

#### Input Parameters
- customer_id: string
- new_date: date — the confirmed new pickup date

### Expected Output

{"status": "Rescheduled", "newDate": "2026-06-23", "confirmationId": "RS-C001-20260623"}

### Error Handling
- On unavailable date: suggest alternative, offer manual escalation
- Never retry a reschedule without re-confirming new date with caller

### Tool Detail: RouteDelayTool

#### Purpose
Retrieves real-time delay information for the waste collection route assigned to the authenticated customer's area.
#### Input Parameters
- route_id: string — derived from customer_id lookup

### Expected Output
// Delay exists {"delay": "2 hours", "reason": "Traffic congestion", "eta": "2:00 PM"} // No delay {"delay": "0", "reason": "On schedule", "eta": "Expected before 12 PM"}

### Tool Detail: ServiceRequestTool
#### Purpose
Creates a formal complaint or service request ticket. Used by Complaint Agent. Always generates a ticket ID even if the underlying issue cannot be resolved immediately.

#### Input Parameters
- customer_id: string
- complaint_description: string — free-form text from caller

### Expected Output
{"ticketId": "CMP-C001-20260620", "status": "Created", "followUp": "24 hours"}

#### Error Handling
- On API failure in Complaint Agent: escalate immediately — never ask frustrated caller to retry
