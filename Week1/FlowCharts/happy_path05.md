## Happy Path 05 (Blockage)
```mermaid
%%{init: {'theme':'base','themeVariables':{
'primaryColor':'#E8F5E9',
'primaryBorderColor':'#1B5E20',
'primaryTextColor':'#000000',
'lineColor':'#000000'
}}}%%

flowchart TD

    A([Citizen Starts Conversation])
    --> B[Root Agent Greets Citizen]

    B --> C[Collect Citizen ID / Registered Phone Number]

    C --> D{Citizen Validated?}

    D -->|Yes| E[Identify Intent]
    D -->|No| F[Retry Authentication]

    E --> G[Intent = Report Blockage]

    G --> H[Route to Blockage Management Agent]

    H --> I[Collect Blockage Location]

    I --> J[Collect Blockage Type]

    J --> K[Collect Additional Details]

    K --> L[Validate Information]

    L --> M[Generate Blockage Report Summary]

    M --> N{Citizen Confirms?}

    N -->|Yes| O[Create Blockage Ticket]

    N -->|Modify Details| I

    O --> P[Submit Report to Operations Team]

    P --> Q[Generate Reference Number]

    Q --> R[Provide Estimated Resolution Time]

    R --> S[Send Confirmation]

    S --> T{Need Additional Assistance?}

    T -->|Yes| U[Route Back to Root Agent]

    T -->|No| V([End Session])

```
