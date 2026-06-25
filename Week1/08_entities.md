### Overview

Entities are structured pieces of data extracted from customer speech. CX uses two types: system entities (built-in, no configuration needed) and custom entities (defined specifically for this project). Entities power slot filling — the process of collecting required information before an API can be called.

|Entity| Type| Used In| Example| Why This Entity|
|------|-----|--------|--------|----------------|
|@sys.phone-number| System| Authentication Agent — phone collection| '9876543210'|Built-in, handles format variations automatically|
|@sys.date |System| Reschedule Agent, Missed Pickup Agent| 'next Monday', 'June 25th'| Resolves relative dates like 'tomorrow' and 'next week' automatically|
|@sys.any |System| Auth — address, Complaint — description| 'Baner, Pune', 'They missed me 3 times' |Captures free-form speech — ideal for unpredictable text like addresses|
|@customer_id| Custom| All Sub-Agents (from session)| 'C001', 'C002'| Maps internal ID format — not collected from caller, set from session|
|@service_area| Custom| Address validation, routing| 'Baner', 'Wakad', 'Hinjewadi'| Known service areas in Pune — helps with address standardization|
|@confirmation| Custom| Reschedule Agent (confirm gate)| 'yes', 'correct', 'that is right'| Captures affirmative/negative responses for confirmation gates|
|@issue_type| Custom| Complaint Agent, Blockage Agent| 'blockage', 'missed', 'delay' |Classifies issue for ticket categorization|

### System Entity: @sys.phone-number
Google's built-in phone number entity handles all common phone number formats automatically. It recognizes both spoken formats ('nine eight seven six five four three two one zero') and typed formats ('9876543210'). It handles international prefixes (+91) and normalizes to a consistent format.
#### Configuration
- No configuration needed — available by default in all CX agents
- Add to Auth Agent form param: Display name: phone_number, Entity: @sys.phone-number, Required: YES

### System Entity: @sys.date
Google's built-in date entity resolves relative and absolute date expressions based on the agent's configured timezone (Asia/Kolkata for this project). This is critical for the Reschedule Agent and Missed Pickup Agent.
#### Examples

|Customer Says| Resolved To (Asia/Kolkata)|
|-------------|---------------------------|
|next Monday |2026-06-23|
|tomorrow |2026-06-21 (if today is June 20)|
|June 25th |2026-06-25|
|this Thursday |2026-06-25|
|the day after tomorrow |2026-06-22|

### System Entity: @sys.any
Captures everything the customer says as free-form text. Used for service address (where predictions are unreliable due to ASR errors) and complaint descriptions (which are completely unpredictable).

#### Important Note

|WARNING: @sys.any MUST be the last parameter collected @sys.any matches EVERYTHING including silence. If used before other parameters, it will swallow the rest of the conversation. Always collect structured params (phone, date) before free-form params (address, description). Address is always collected AFTER phone_number in the Auth Agent.|
|-----------------------------------|

### Custom Entity: @service_area
A custom entity listing known service areas in Pune. Used to help standardize address input and reduce ASR errors during authentication.

#### Values and Synonyms

|Entity Value| Synonyms|
|------------|---------|
|baner| Baner, Baner Pune, near Baner|
|wakad| Wakad, Wakad Pune, near Wakad|
|hinjewadi| Hinjewadi, Hinjewadi Pune, Hinjewadi IT Park area|
|aundh| Aundh, Aundh Pune, near Aundh|

### Custom Entity: @confirmation
Captures yes/no affirmation for confirmation gates — primarily used in the Reschedule Agent before executing a schedule change.

#### Values
|Entity Value| Training Synonyms|
|------------|------------------|
|yes| yes, correct, that is right, exactly, confirmed, go ahead, proceed, affirmative, yes please, that is correct|
|no| no, wrong, incorrect, that is not right, cancel, stop, that is wrong, negative, no thank you, hold on| 
