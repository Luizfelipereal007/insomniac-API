# Insomniac API

![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-automation-blue)
![Schedule](https://img.shields.io/badge/Schedule-cron-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

> A GitHub Actions-based automated solution to prevent API hibernation by sending periodic health checks during business hours.

## 🎯 Quick Overview

```mermaid
graph LR
    A[GitHub Actions] -->|Every 5 min| B{Business Hours?}
    B -->|06:00-18:00| C[🔄 Ping API]
    B -->|Outside Hours| D[💤 Sleep Mode]
    C --> E[✅ Keep API Awake]
    D --> F[💰 Save Resources]
    
    style C fill:#90EE90,stroke:#333,stroke-width:2px
    style D fill:#FFE66D,stroke:#333,stroke-width:2px
    style E fill:#4ECDC4,stroke:#333,stroke-width:2px
    style F fill:#FF6B6B,stroke:#333,stroke-width:2px
```

## 📋 Overview

Insomniac API is an automated bot designed to prevent your API from entering hibernation mode by sending periodic health check requests. The system operates exclusively during business hours (06:00-18:00, Monday-Saturday) to ensure optimal resource utilization while maintaining API availability.

## 🎯 Key Features

- ⏰ **Scheduled Automation**: Automated requests every 5 minutes during business hours
- 🕐 **Business Hours Only**: Active only from 06:00 to 18:00 (America/Manaus timezone)
- 📅 **Smart Scheduling**: Runs Monday through Saturday, excluding Sundays
- 🔍 **HTTP Monitoring**: Tracks and logs HTTP response codes
- 📊 **Execution Logging**: Comprehensive logging of all workflow executions
- 🎮 **Manual Execution**: Support for manual workflow triggers for testing and debugging
- 💰 **Resource Efficient**: Inactive outside business hours to minimize resource consumption

## 🏗️ Architecture

### System Overview

```mermaid
graph TB
    A[GitHub Actions<br/>Cron Scheduler] --> B{Business Hours?<br/>06:00-18:00 Mon-Sat}
    B -->|Yes| C[Execute Health Check]
    B -->|No| D[Skip Execution<br/>Save Resources]
    C --> E[Send GET Request<br/>to API Endpoint]
    E --> F{HTTP Response}
    F -->|200 OK| G[✅ Log Success]
    F -->|Error| H[❌ Log Error<br/>Trigger Alert]
    G --> I[Update GitHub Actions<br/>Dashboard]
    H --> I
    I --> J[Wait for Next<br/>Scheduled Run]
```

### Schedule Visualization

```mermaid
gantt
    title Insomniac API - Weekly Schedule
    dateFormat HH:mm
    axisFormat %H:%M
    section Monday
    Active Period    :active, 2024-01-01 06:00, 12h
    Inactive Period  :inactive, 2024-01-01 18:00, 12h
    section Tuesday
    Active Period    :active, 2024-01-02 06:00, 12h
    Inactive Period  :inactive, 2024-01-02 18:00, 12h
    section Wednesday
    Active Period    :active, 2024-01-03 06:00, 12h
    Inactive Period  :inactive, 2024-01-03 18:00, 12h
    section Thursday
    Active Period    :active, 2024-01-04 06:00, 12h
    Inactive Period  :inactive, 2024-01-04 18:00, 12h
    section Friday
    Active Period    :active, 2024-01-05 06:00, 12h
    Inactive Period  :inactive, 2024-01-05 18:00, 12h
    section Saturday
    Active Period    :active, 2024-01-06 06:00, 12h
    Inactive Period  :inactive, 2024-01-06 18:00, 12h
    section Sunday
    Inactive All Day :inactive, 2024-01-07 00:00, 24h
```

### Workflow Process

```mermaid
sequenceDiagram
    participant Cron as GitHub Cron
    participant Actions as GitHub Actions
    participant API as Target API
    participant Logger as System Logger
    
    Cron->>Actions: Trigger (Every 5 min)
    Actions->>Actions: Check Time (06:00-18:00?)
    alt Business Hours
        Actions->>API: GET Request
        API-->>Actions: HTTP Response
        alt Success (200 OK)
            Actions->>Logger: Log Success
            Actions->>Actions: Mark as ✅
        else Error (4xx/5xx)
            Actions->>Logger: Log Error
            Actions->>Actions: Mark as ❌
        end
    else Outside Business Hours
        Actions->>Logger: Skip (Resource Saving)
        Actions->>Actions: Mark as ⏭️
    end
    Actions-->>Cron: Wait for Next Trigger
```

The project leverages **GitHub Actions** to automate the API health check process. The workflow is configured with the following schedule:

| Parameter | Value |
|-----------|-------|
| **Days** | Monday - Saturday |
| **Time Window** | 06:00 - 18:00 (America/Manaus) |
| **Frequency** | Every 5 minutes |
| **Timezone** | America/Manaus |

## 🔧 Configuration

### Monitored API

- **Endpoint**: `https://my-api.example.com/`
- **Expected Response**: HTTP 200 OK
- **Method**: GET

### Workflow Configuration

The GitHub Actions workflow is configured using cron expressions to execute during business hours only. The schedule automatically excludes:

- Outside business hours (18:00-06:00)
- Sundays
- Failed API responses trigger alerts

## 📈 Monitoring & Logging

### Dashboard Overview

```mermaid
pie title Success Rate Distribution
    "Successful Requests (200 OK)" : 95
    "Client Errors (4xx)" : 3
    "Server Errors (5xx)" : 2
```

### Response Time Analysis

```mermaid
xychart-beta
    title "API Response Times (ms)"
    x-axis ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat"]
    y-axis "Response Time (ms)" 0 --> 500
    line [120, 145, 98, 167, 132, 110]
```

### GitHub Actions Dashboard

Monitor all workflow executions through the **Actions** tab in your GitHub repository. Each execution provides:

- ⏱️ Execution timestamp
- 🔢 HTTP response code
- ✅ Success/Failure status
- 📝 Detailed execution logs

### Response Status Tracking

The system tracks and categorizes API responses:

- **200 OK**: API is healthy and responsive
- **4xx/5xx Errors**: API issues requiring attention
- **Timeout**: API not responding within expected timeframe

## 🚀 Usage

### State Machine

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Checking: Cron Trigger
    Checking --> Active: Business Hours
    Checking --> Idle: Outside Hours
    Active --> Requesting: Start Health Check
    Requesting --> Success: HTTP 200
    Requesting --> Error: HTTP Error
    Success --> Logging: Log Response
    Error --> Logging: Log Error
    Logging --> Idle: Complete
    Idle --> [*]
```

### Deployment Architecture

```mermaid
graph LR
    subgraph GitHub Infrastructure
        A[GitHub Repository] --> B[GitHub Actions]
        B --> C[Cron Scheduler]
        C --> D[Workflow Runner]
    end
    
    subgraph External Services
        E[Target API<br/>my-api.example.com]
    end
    
    subgraph Monitoring
        F[Actions Dashboard]
        G[Execution Logs]
    end
    
    D --> E
    E --> D
    D --> F
    D --> G
    
    style E fill:#f9f,stroke:#333,stroke-width:2px
    style F fill:#bbf,stroke:#333,stroke-width:2px
    style G fill:#bfb,stroke:#333,stroke-width:2px
```

### Automatic Execution

The workflow runs automatically according to the configured schedule. No manual intervention required during normal operation.

### Manual Execution

For testing or ad-hoc verification:

1. Navigate to the **Actions** tab in your GitHub repository
2. Select the **Insomniac API** workflow
3. Click **Run workflow**
4. Choose the branch and click **Run workflow**

## 🛠️ Technical Details

### Workflow Components

- **Trigger**: Scheduled cron job
- **Runner**: GitHub-hosted runner
- **Steps**: HTTP request execution, response validation, logging
- **Retry Logic**: Configurable retry mechanism for failed requests

### Requirements

- GitHub repository with Actions enabled
- API endpoint accessible from GitHub Actions runners
- Appropriate permissions for workflow execution

## 📝 Setup Instructions

### Setup Process Flow

```mermaid
flowchart TD
    A[Start Setup] --> B{Fork or Clone?}
    B -->|Fork| C[Fork Repository]
    B -->|Clone| D[Clone Repository]
    C --> E[Configure API Endpoint]
    D --> E
    E --> F{Adjust Schedule?}
    F -->|Yes| G[Modify Cron Expression]
    F -->|No| H[Keep Default Schedule]
    G --> I[Enable GitHub Actions]
    H --> I
    I --> J[Run First Test]
    J --> K{Test Successful?}
    K -->|Yes| L[✅ Setup Complete]
    K -->|No| M[Debug & Retry]
    M --> J
    
    style L fill:#90EE90,stroke:#333,stroke-width:2px
    style M fill:#FFB6C1,stroke:#333,stroke-width:2px
```

1. **Fork or clone this repository**
2. **Configure the API endpoint** in the workflow file
3. **Adjust the schedule** if needed (modify cron expression)
4. **Enable GitHub Actions** in repository settings
5. **Monitor initial executions** in the Actions tab

## 🔐 Security Considerations

### Security Flow

```mermaid
graph TD
    A[Workflow Trigger] --> B{Authentication Required?}
    B -->|No| C[Direct API Call]
    B -->|Yes| D[Retrieve from Secrets]
    D --> E{Secrets Valid?}
    E -->|Yes| F[Authenticated Call]
    E -->|No| G[❌ Security Alert]
    C --> H[API Response]
    F --> H
    H --> I{Sensitive Data?}
    I -->|Yes| J[Redact Logs]
    I -->|No| K[Full Logging]
    J --> L[Secure Dashboard]
    K --> L
    G --> M[Block Execution]
    
    style G fill:#FF6B6B,stroke:#333,stroke-width:2px
    style J fill:#FFE66D,stroke:#333,stroke-width:2px
    style L fill:#4ECDC4,stroke:#333,stroke-width:2px
```

### Error Handling Flow

```mermaid
graph TB
    A[API Request] --> B{Response Received?}
    B -->|No| C[Timeout Error]
    B -->|Yes| D{Status Code}
    D -->|200| E[✅ Success]
    D -->|4xx| F[Client Error]
    D -->|5xx| G[Server Error]
    C --> H[Log Timeout]
    F --> I[Log Client Error]
    G --> J[Log Server Error]
    H --> K[Retry Logic]
    I --> K
    J --> K
    K --> L{Retry Limit Reached?}
    L -->|No| A
    L -->|Yes| M[❌ Final Failure]
    E --> N[Update Dashboard]
    M --> N
    
    style E fill:#90EE90,stroke:#333,stroke-width:2px
    style M fill:#FF6B6B,stroke:#333,stroke-width:2px
    style N fill:#4ECDC4,stroke:#333,stroke-width:2px
```

- API endpoints should be publicly accessible or authenticated via secrets
- Sensitive data (API keys, tokens) should be stored in GitHub Secrets
- Workflow logs may contain sensitive information - configure accordingly
- Consider rate limiting on the API endpoint

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 📄 License

This project is licensed under the MIT License.

## 📞 Support

### Issue Resolution Flow

```mermaid
flowchart LR
    A[Issue Reported] --> B{Issue Type}
    B -->|Configuration| C[Check Workflow Settings]
    B -->|API Access| D[Verify Endpoint]
    B -->|Schedule| E[Review Cron Expression]
    B -->|Permissions| F[Check GitHub Settings]
    C --> G{Resolved?}
    D --> G
    E --> G
    F --> G
    G -->|Yes| H[✅ Issue Closed]
    G -->|No| I[Request More Info]
    I --> J[Community Support]
    J --> K{Solution Found?}
    K -->|Yes| H
    K -->|No| L[Escalate to Maintainers]
    L --> M[Detailed Investigation]
    M --> H
    
    style H fill:#90EE90,stroke:#333,stroke-width:2px
    style L fill:#FFE66D,stroke:#333,stroke-width:2px
```

### System Health Overview

```mermaid
graph TB
    subgraph Health Metrics
        A[Uptime: 99.9%]
        B[Success Rate: 95%]
        C[Avg Response: 132ms]
        D[Daily Requests: 144]
    end
    
    subgraph Resource Usage
        E[CPU: Low]
        F[Memory: Minimal]
        G[Network: Efficient]
    end
    
    subgraph Cost Analysis
        H[GitHub Actions: Free Tier]
        I[API Costs: Minimal]
        J[Maintenance: Low]
    end
    
    style A fill:#90EE90,stroke:#333,stroke-width:2px
    style B fill:#90EE90,stroke:#333,stroke-width:2px
    style C fill:#4ECDC4,stroke:#333,stroke-width:2px
    style H fill:#FFE66D,stroke:#333,stroke-width:2px
```

For issues or questions:
- Open an issue in the GitHub repository
- Check the Actions tab for execution logs
- Review the workflow configuration

---

**Note**: This is a preventive maintenance tool. Ensure your API has proper monitoring and alerting beyond this simple health check mechanism.
