# Technical Strategy

This section outlines the architectural decisions and design principles that form the foundation of AMBA. Understanding these concepts is crucial for effective implementation and customization of the framework.

## Design Principles

AMBA's architecture is built on these core principles:

1. **Policy-Driven Governance**
   - Automated enforcement of monitoring standards
   - Consistent application across resources
   - Scalable management through policy definitions

2. **Standardized Monitoring**
   - Pre-defined alert patterns based on best practices
   - Uniform threshold management
   - Consistent naming and tagging conventions

3. **Flexible Integration**
   - Seamless connection with Azure services
   - Extensible monitoring patterns
   - Support for custom requirements

## Architecture Components

### 1. Policy Framework

The policy framework is the primary mechanism for implementing and managing monitoring standards:

```json
{
  "policyDefinition": {
    "properties": {
      "policyType": "Custom",
      "mode": "All",
      "parameters": {},
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Compute/virtualMachines"
            }
          ]
        },
        "then": {
          "effect": "deployIfNotExists",
          "details": {
            "type": "Microsoft.Insights/metricAlerts",
            "roleDefinitionIds": [
              "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "existenceCondition": {
              "allOf": [
                {
                  "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria.allOf[*].metricNamespace",
                  "equals": "Microsoft.Compute/virtualMachines"
                }
              ]
            }
          }
        }
      }
    }
  }
}
```

### 2. Monitoring Components

AMBA organizes monitoring into distinct components:

#### Alert Rules Engine
```json
{
  "alertRule": {
    "type": "Microsoft.Insights/metricAlerts",
    "properties": {
      "severity": 2,
      "enabled": true,
      "evaluationFrequency": "PT5M",
      "windowSize": "PT15M",
      "criteria": {
        "allOf": [
          {
            "threshold": 90,
            "name": "High CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "metricName": "Percentage CPU",
            "operator": "GreaterThan",
            "timeAggregation": "Average"
          }
        ]
      }
    }
  }
}
```

#### Analytics Integration
```kusto
// Sample monitoring query pattern
let threshold = 90;
Perf
| where ObjectName == "Processor"
| where CounterName == "% Processor Time"
| where CounterValue > threshold
| summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 5m)
```

### 3. Resource Organization

AMBA implements a hierarchical resource organization:

```plaintext
Root Management Group
├── Platform Management Group
│   ├── Connectivity Subscription
│   │   └── Network Monitoring
│   ├── Management Subscription
│   │   └── Log Analytics
│   └── Identity Subscription
│       └── Identity Monitoring
└── Landing Zones
    └── Workload Subscriptions
        └── Application Monitoring
```

## Integration Patterns

### 1. Azure Monitor Integration

```json
{
  "workspaceProfile": {
    "name": "amba-workspace",
    "properties": {
      "retentionInDays": 30,
      "features": {
        "searchVersion": 1,
        "enableLogAccessUsingOnlyResourcePermissions": true
      }
    }
  }
}
```

### 2. Action Groups Configuration

```json
{
  "actionGroup": {
    "properties": {
      "groupShortName": "AMBAAlerts",
      "enabled": true,
      "emailReceivers": [...],
      "smsReceivers": [...],
      "webhookReceivers": [
        {
          "name": "ServiceNow",
          "serviceUri": "https://service-now.example.com/webhook",
          "useCommonAlertSchema": true
        }
      ]
    }
  }
}
```

## Customization Framework

AMBA provides several customization points:

### 1. Alert Threshold Management
```json
{
  "thresholdOverrides": {
    "tagName": "_amba-{metricName}-threshold-override_",
    "scope": "resource",
    "inheritance": "none"
  }
}
```

### 2. Monitoring Exclusions
```json
{
  "exclusionTags": {
    "name": "MonitorDisable",
    "value": "true",
    "effect": "disable"
  }
}
```

## Scaling Considerations

### 1. Resource Limits

- Maximum alerts per subscription: 5000
- Maximum action groups per subscription: 2000
- Maximum conditions per alert rule: 20

### 2. Performance Optimization

```json
{
  "evaluationFrequency": "PT5M",
  "windowSize": "PT15M",
  "throttling": {
    "duration": "PT24H",
    "threshold": 100
  }
}
```

## Next Steps

Ready to implement these architectural patterns? Continue to our [ALZ Integration](03-ALZ-Integration.md) section to learn how to integrate AMBA with Azure Landing Zones.

[Back to Main Document](../AMBA%20Design%20Document.md) | [Previous: Getting Started](01-Getting-Started.md) | [Next: ALZ Integration](03-ALZ-Integration.md) 