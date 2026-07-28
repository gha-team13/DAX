```mermaid
%%{ init: { 'flowchart': { 'curve': 'step' }, 'themeVariables': { 'edgeLabelBackground': 'transparent' } } }%%
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

    %% Cross-subgraph connections
    B -->|"Duration: 48 Hours"| C
    D -->|"Yes<br>(Duration: 24 Hours)"| E
    G -->|"Yes<br>(Duration: 7 Days)"| H

    %% Node Styles (Background fills restored)
    classDef validate stroke:#003F69,fill:#ffffff,color:#333333,stroke-width:2px
    classDef recreated stroke:#4D4D4D,fill:#ffffff,color:#333333,stroke-width:2px
    classDef transferred stroke:#008A00,fill:#ffffff,color:#333333,stroke-width:2px
    classDef removed stroke:#D6A800,fill:#ffffff,color:#333333,stroke-width:2px
    classDef decision stroke:#008A00,fill:#E5F3E5,color:#000000,stroke-width:2px
    classDef remediation stroke:#E32636,fill:#FCE9EB,color:#333333,stroke-width:2px

    %% Assign Classes
    class A,B validate
    class C recreated
    class E,F transferred
    class H removed
    class D,G decision
    class R1,R2 remediation

    %% Subgraph Styles (Background fills restored)
    style Validate fill:#E6F0F5,stroke:#003F69,stroke-width:2px,stroke-dasharray: 5 5
    style Recreated fill:#F2F2F2,stroke:#4D4D4D,stroke-width:2px,stroke-dasharray: 5 5
    style Transferred fill:#E5F3E5,stroke:#008A00,stroke-width:2px,stroke-dasharray: 5 5
    style Removed fill:#FFFBE5,stroke:#D6A800,stroke-width:2px,stroke-dasharray: 5 5
