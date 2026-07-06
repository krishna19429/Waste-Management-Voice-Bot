### 1. Authentication Failure
**Scenario:** User provides incorrect authentication details.<br>
**Fallback:** Allow up to 3 retry attempts. If authentication still fails, escalate the conversation to a human agent.

### 2. Unknown User Query
**Scenario:** User asks a question outside supported intents (Missed Pickup, Reschedule, Registration).<br>
**Fallback:** Inform the user that the request is not supported and route to a human agent if needed.

### 3. Missing User Information
**Scenario:** User does not provide required details such as service date or registration information.<br>
**Fallback:** Prompt the user again for the missing information before proceeding.

### 4. Invalid Reschedule Date
**Scenario:** User enters a date that is unavailable, invalid, or in the past.<br>
**Fallback:** Ask the user to provide another valid pickup date.

### 5. Service Date Not Found
**Scenario:** The provided service date does not match any record in the database.<br>
**Fallback:** Request another date and perform database verification again.

### 6. No Response from User
**Scenario:** User becomes inactive during the conversation.<br>
**Fallback:** Send a reminder prompt. After multiple no-response attempts, end the session or transfer to a human agent.
