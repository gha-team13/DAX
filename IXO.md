```mermaid
%%{ init: { 'flowchart': { 'curve': 'step' } } }%%
flowchart LR
    subgraph Validate["Validate"]
        direction TB
        A["1. Confirm impacted areas<br><br>Identify impacted Segments, applications, platforms, ICTO and Senior Manager contacts"]
        B["2. Validate target-state access<br><br>Confirm required Release, Distributed, Mainframe, VDI, AD, CyberArk/NPID, pipeline, Linux/Unix and ServiceNow access"]
        
        %% Internal connection
        A --> B
    end

    subgraph Recreated["Recreated"]
        direction TB
        C["3. Prepare replacement access<br><br>Create or transfer AD groups, CyberArk, VDI, pipeline, Linux/Unix and workflow access"]
        D{"Replacement access<br>ready and validated?"}
        R1["Complete missing replacement access"]
        
        %% Internal connections
        C --> D
        D -->|No| R1
        R1 --> C
    end

    subgraph Transferred["Transferred"]
        direction TB
        E["4. Execute Workday move<br><br>CyberArk/NPID access may be removed by design"]
        F["5. Activate receiving-team access<br><br>Receiving manager and target teams activate and validate access"]
        G{"Receiving-team access<br>confirmed?"}
        R2["Resolve receiving-team access gaps"]
        
        %% Internal connections
        E --> F
        F --> G
        G -->|No| R2
        R2 --> F
    end

    subgraph Removed["Removed"]
        direction TB
        H["6. Clean up legacy ARE access<br><br>Remove obsolete ARE groups, IDs, mappings, VDI access and permissions"]
    end

    %% Cross-subgraph connections (Now with time durations)
    %% You can edit the text inside the quotes to change the times
    B -->|"Duration: 48 Hours"| C
    D -->|"Yes<br>(Duration: 24 Hours)"| E
    G -->|"Yes<br>(Duration: 7 Days)"| H

    classDef validate stroke:#818cf8,fill:#eef2ff
    classDef recreated stroke:#2dd4bf,fill:#f0fdfa
    classDef transferred stroke:#4ade80,fill:#f0fdf4
    classDef removed stroke:#fb923c,fill:#fff7ed
    classDef decision stroke:#a3e635,fill:#f7fee7
    classDef remediation stroke:#fb7185,fill:#fff1f2

    class A,B validate
    class C recreated
    class E,F transferred
    class H removed
    class D,G decision
    class R1,R2 remediation
