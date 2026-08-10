```mermaid
---
config:
  layout: elk
  flowchart:
    curve: stepBefore
---
flowchart LR
    subgraph legacy["LEGACY / CURRENT STATE (HIGH RISK & TECHNICAL DEBT)"]
        oldWS["Standard Laptops / Admin Desktops<br/>(Direct Connections)"]
        oldScripts["Bespoke In-House Scripts<br/>• Failed secure code reviews<br/>• Unsupported custom code<br/>• Direct high-privilege queries"]
        oldDCS["On-Premises AD Domain Controllers<br/>[Live Production Directory Services]"]
        oldOutput["Manual, Scattered Spreadsheets<br/>• Highly error-prone<br/>• No lifecycle visibility<br/>• Fails FND-59999 audits"]
    end

    subgraph target["NEW / TARGET STATE (QUEST ADQER COMPLIANT STATE)"]
        subgraph access["1. Access Zone (AAL2 Compliance)"]
            newClient["Report Manager Thick Client<br/>(Desktop Management GUI)<br/>[AAL2: MFA / Windows Hello]"]
            newCyberArk["CyberArk PAM / Jump Server<br/>(Bastion Host for Administrators)<br/>[Session Logging & Vaulted Credentials]"]
        end

        subgraph appzone["2. Standard App Zone"]
            newPrimary["Primary Discovery Manager<br/>[ADQER Server (TDBFG Forest)]<br/>(Windows Server 2022)"]

            subgraph nodes["Clustered Local Collection Nodes (Load Sharing)"]
                nodeTD["TD Securities Node<br/>(Local Queries)"]
                nodeRNET["RNET Node<br/>(Local Queries)"]
                nodeBKNG["BKNG Node<br/>(Local Queries)"]
                nodeMM["MelocheMonnex Node<br/>(Local Queries)"]
            end

            targetDCS["On-Premises AD Domain Controllers<br/>[EWF G, BKNG, MMI Domains]<br/>(Queried Locally)"]
        end

        subgraph dbzone["3. Database Zone"]
            newSQL["Central SQL Server 2022 DB<br/>[Offline Repository]<br/>• Confidential (No passwords/hashes)<br/>• Vaulted Encryption Key (DR Portable)<br/>• VMware Hypervisor Auto-Reboot (Cost Saving)"]
        end

        subgraph downstream["4. Integration, Analytics, & Compliance Tier"]
            newTIBCO["TIBCO Integration Broker<br/>(SCIM/JSON Payloads & Throttling)"]
            newPowerBI["Power BI Dashboards<br/>(CSV Ingestion for AMH & EE)"]
            newSplunk["Splunk SIEM<br/>(Operational & Audit Logs)<br/>Index: idx_iam_discovery"]
            newCloudIAM["Cloud IAM on Demand<br/>(Entra ID / Okta)<br/>[Access Enforcement]"]
        end
    end

    subgraph legend["LEGEND"]
        direction TB
        legendLegacy["Legacy / Current State"]
        legendTarget["New / Target State"]
        legendAccess["Access Zone"]
        legendApp["Application Zone"]
        legendDatabase["Database Zone"]
        legendDownstream["Integration, Analytics, & Compliance"]

        normalStart["Normal connector"] -->|Standard data or control flow| normalEnd[" "]
        dashedStart["Dashed connector"] -.->|Read-only or conditional flow| dashedEnd[" "]
        transitionStart["Transition connector"] ==>|Migration or compliance transition| transitionEnd[" "]
    end

    oldWS -->|Launches scripts locally| oldScripts
    oldScripts -->|Direct live LDAP queries<br/>Creates massive 12-15 hr load| oldDCS
    oldScripts -->|Outputs raw files locally| oldOutput

    newClient -->|GUI Management<br/>AD Group RBAC| newPrimary
    newCyberArk -->|Secure Admin RDP<br/>Session Logged| newPrimary

    newPrimary -->|Orchestrates Scan| nodeTD
    newPrimary -->|Orchestrates Scan| nodeRNET
    newPrimary -->|Orchestrates Scan| nodeBKNG
    newPrimary -->|Orchestrates Scan| nodeMM

    nodeTD -.->|Read-Only Query| targetDCS
    nodeRNET -.->|Read-Only Query| targetDCS
    nodeBKNG -.->|Read-Only Query| targetDCS
    nodeMM -.->|Read-Only Query| targetDCS

    nodeTD -->|Offline Aggregation| newSQL
    nodeRNET -->|Offline Aggregation| newSQL
    nodeBKNG -->|Offline Aggregation| newSQL
    nodeMM -->|Offline Aggregation| newSQL
    newPrimary -->|Direct SQL Connection| newSQL

    newSQL -->|Direct Query<br/>Delta changes only| newTIBCO
    newPrimary -->|Automated Scheduled<br/>CSV Flat-File Export| newPowerBI
    newPrimary -->|Operational & Audit<br/>Event Forwarding| newSplunk
    newTIBCO -->|Encrypted REST API<br/>SCIM Payloads| newCloudIAM

    oldDCS ==>|TRANSITION TO ADQER COMPLIANCE<br/>Satisfies FND-59999 Items 6 & 7| targetDCS

    classDef legacyStyle stroke:#C0392B,fill:#FADBD8,stroke-width:2px,color:#000
    classDef accessStyle stroke:#27AE60,fill:#D5F5E3,stroke-width:1.5px,color:#000
    classDef appStyle stroke:#2ECC71,fill:#EAFAF1,stroke-width:1.5px,color:#000
    classDef nodeStyle stroke:#F1C40F,fill:#FCF3CF,stroke-width:1px,color:#000
    classDef databaseStyle stroke:#2980B9,fill:#EAF2F8,stroke-width:1.5px,color:#000
    classDef downstreamStyle stroke:#8E44AD,fill:#F5EEF8,stroke-width:1.5px,color:#000
    classDef targetStyle stroke:#117A65,fill:#E8F8F5,stroke-width:2px,color:#000
    classDef legendText fill:#FFFFFF,stroke:#5C5C5C,stroke-width:1px,color:#000

    class oldWS,oldScripts,oldDCS,oldOutput legacyStyle
    class newClient,newCyberArk accessStyle
    class newPrimary,targetDCS appStyle
    class nodeTD,nodeRNET,nodeBKNG,nodeMM nodeStyle
    class newSQL databaseStyle
    class newTIBCO,newPowerBI,newSplunk,newCloudIAM downstreamStyle
    class legendLegacy legacyStyle
    class legendTarget targetStyle
    class legendAccess accessStyle
    class legendApp appStyle
    class legendDatabase databaseStyle
    class legendDownstream downstreamStyle
    class normalStart,normalEnd,dashedStart,dashedEnd,transitionStart,transitionEnd legendText

    style legacy fill:#FADBD8,stroke:#C0392B,stroke-width:2.5px
    style target fill:#E8F8F5,stroke:#117A65,stroke-width:2.5px
    style access fill:#D5F5E3,stroke:#27AE60,stroke-width:1.5px
    style appzone fill:#EAFAF1,stroke:#2ECC71,stroke-width:1.5px
    style nodes fill:#F4FBF7,stroke:#2ECC71,stroke-width:1px,stroke-dasharray:5 5
    style dbzone fill:#EAF2F8,stroke:#2980B9,stroke-width:1.5px
    style downstream fill:#F5EEF8,stroke:#8E44AD,stroke-width:1.5px
    style legend fill:#FFFFFF,stroke:#5C5C5C,stroke-width:1.5px
