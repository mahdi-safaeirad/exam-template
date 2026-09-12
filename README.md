# Scenario 2

Draw the **output** system. You can use AI.
Then explain your code. 

English is better. Persian is OK.

## Architecture
# Scenario 2

Draw the **output** system. You can use AI.
Then explain your code.

English is better. Persian is OK.

## Architecture

```mermaid
flowchart TB
    subgraph VM["Second VM (95.38.188.153)"]
        NE[node_exporter :9100]
        PROM[Prometheus :9090]
        GRAF[Grafana :3000]
    end

    User([User / Browser]) --> GRAF
    GRAF -->|datasource| PROM
    PROM -->|scrape| NE
    PROM -->|scrape| PROM

## Code

### Playbook & Roles

Explain the playbook or roles you have created. 
For example: 
+ `package`: Install requirements

### Inventory
Explain your Inventory if needed

## Credentials / Login
Add any login or credential data here. For example
```
# Grafana
user: admin
pass: admin
```

# Challenges

Write one item for each challenge. What broke, and how you fixed it.
For example: 

+ **Internet Connection**: Iran block downloading from dockerhub 
+ **Access to VM** is not available through my network
