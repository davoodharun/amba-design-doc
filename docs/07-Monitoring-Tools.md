# Monitoring Tools Integration

AMBA's integration with various monitoring tools provides a comprehensive approach to monitoring your Azure environment. This section explores how AMBA can be integrated with Azure Workbooks, Application Gateway Insights, Network Health Insights, Virtual Machine Monitoring, and Backup Monitoring to deliver a unified monitoring experience. For more information, see [Azure Monitor tools overview](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/monitoring-overview).

## Azure Workbooks

Azure Workbooks provide a powerful platform for creating interactive and dynamic dashboards that visualize your monitoring data. For detailed information, see [Azure Workbooks documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/workbooks-overview).

### Alert Management Workbook

The Alert Management Workbook is designed to provide a comprehensive overview of your alert status. This workbook includes sections for active alerts, alert trends, and resolution times, helping you quickly identify and address issues.

**Example Workbook Template**:
```json
{
  "version": "Notebook/1.0",
  "items": [
    {
      "type": "query",
      "name": "Alert Overview",
      "query": "AlertsManagementResources\n| where type == 'microsoft.alertsmanagement/alerts'\n| summarize Count=count() by severity, status",
      "timeContext": {
        "durationMs": 86400000
      },
      "queryType": 1,
      "visualization": "piechart"
    }
  ]
}
```

**Reference**: [Alert Management Workbook samples](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Workbooks/AlertManagement)

### Resource Health Workbook

The Resource Health Workbook focuses on the health status of your Azure resources. For more information, see [Resource Health overview](https://learn.microsoft.com/en-us/azure/service-health/resource-health-overview).

**Example Health Query**:
```kusto
AzureActivity
| where CategoryValue == 'ResourceHealth'
| summarize count() by ResourceProvider, ResourceType, Status
```

**Reference**: [Resource Health checks documentation](https://learn.microsoft.com/en-us/azure/service-health/resource-health-checks-resource-types)

### Performance Monitoring Workbook

The Performance Monitoring Workbook offers insights into the performance of your Azure resources. For detailed metrics information, see [Azure Monitor metrics overview](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-platform-metrics).

**Example Performance Dashboard**:
```json
{
  "type": "query",
  "name": "CPU Usage",
  "query": "Perf\n| where ObjectName == 'Processor' and CounterName == '% Processor Time'\n| summarize AvgCPU=avg(CounterValue) by Computer\n| order by AvgCPU desc",
  "visualization": "linechart"
}
```

**Reference**: [Performance monitoring templates](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Workbooks/Performance)

## Application Gateway Insights

Application Gateway Insights provide specialized monitoring for your application gateway infrastructure. For comprehensive documentation, see [Application Gateway metrics](https://learn.microsoft.com/en-us/azure/application-gateway/application-gateway-metrics).

### Key Metrics Configuration

**Example Metrics Query**:
```kusto
AppGatewayMetrics
| where TimeGenerated > ago(1h)
| where MetricName in ('FailedRequests', 'HealthyHostCount', 'ResponseTime')
| summarize Value=avg(MetricValue) by MetricName, bin(TimeGenerated, 5m)
```

**Reference**: [Application Gateway monitoring best practices](https://learn.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)

### Health Status Monitoring

**Example Health Check Configuration**:
```json
{
  "probes": [
    {
      "name": "healthProbe",
      "properties": {
        "protocol": "Http",
        "host": "contoso.com",
        "path": "/health",
        "interval": 30,
        "timeout": 30,
        "unhealthyThreshold": 3
      }
    }
  ]
}
```

**Reference**: [Health probe configuration guide](https://learn.microsoft.com/en-us/azure/application-gateway/application-gateway-probe-overview)

## Network Health Insights

Network Health Insights focus on monitoring the health and performance of your network infrastructure. For detailed information, see [Network monitoring solutions](https://learn.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview).

### Connectivity Monitoring

**Example Connection Monitor Configuration**:
```json
{
  "name": "connection-monitor",
  "properties": {
    "endpoints": [
      {
        "name": "source",
        "type": "AzureVM"
      },
      {
        "name": "destination",
        "type": "ExternalAddress"
      }
    ],
    "testConfigurations": [
      {
        "name": "http-test",
        "testFrequencySec": 30,
        "protocol": "Http",
        "successThreshold": 1
      }
    ]
  }
}
```

**Reference**: [Connection Monitor documentation](https://learn.microsoft.com/en-us/azure/network-watcher/connection-monitor-overview)

### Performance Metrics

**Example Network Performance Query**:
```kusto
NetworkPerformance
| where TimeGenerated > ago(1h)
| where NetworkDirection == "Outbound"
| summarize AvgLatency=avg(LatencyMs) by DestinationNetwork
```

**Reference**: [Network performance monitoring](https://learn.microsoft.com/en-us/azure/network-watcher/network-performance-monitor)

## Virtual Machine Monitoring

Virtual Machine Monitoring provides comprehensive insights into the performance and health of your virtual machines. For detailed documentation, see [VM insights overview](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-overview).

### Performance Tracking

**Example VM Metrics Configuration**:
```json
{
  "diagnosticSettings": {
    "metrics": [
      {
        "category": "AllMetrics",
        "enabled": true,
        "retentionPolicy": {
          "days": 30,
          "enabled": true
        }
      }
    ]
  }
}
```

**Reference**: [VM performance monitoring guide](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/monitor-virtual-machine)

### Health Status Monitoring

**Example Health Check Query**:
```kusto
Heartbeat
| where TimeGenerated > ago(15m)
| summarize LastHeartbeat=max(TimeGenerated) by Computer
| extend Status = iff(LastHeartbeat < ago(5m), 'Offline', 'Online')
```

**Reference**: [VM health monitoring](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-health)

## Backup Monitoring

Backup Monitoring focuses on ensuring that your backup operations are successful and efficient. For comprehensive documentation, see [Azure Backup monitoring](https://learn.microsoft.com/en-us/azure/backup/backup-azure-monitoring-built-in-monitor).

### Backup Status Monitoring

**Example Backup Status Query**:
```kusto
CoreAzureBackup
| where OperationName == "Backup"
| summarize LastBackup=max(TimeGenerated) by BackupItemUniqueId
| extend Status = iff(LastBackup < ago(24h), 'Warning', 'Healthy')
```

**Reference**: [Backup monitoring solutions](https://learn.microsoft.com/en-us/azure/backup/backup-azure-monitoring-use-azuremonitor)

### Storage Utilization

**Example Storage Analysis**:
```kusto
AddonAzureBackupStorage
| where TimeGenerated > ago(7d)
| summarize StorageUsed=max(StorageUsedInMBs) by BackupItemUniqueId
| project BackupItem=BackupItemUniqueId, StorageGB=StorageUsed/1024
```

**Reference**: [Backup storage monitoring](https://learn.microsoft.com/en-us/azure/backup/backup-azure-monitoring-built-in-monitor#backup-items-and-backup-jobs)

## Custom Dashboards

Custom dashboards allow you to create personalized views of your monitoring data. For detailed information, see [Azure dashboards documentation](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards).

### Layout and Design

**Example Dashboard Configuration**:
```json
{
  "properties": {
    "lenses": {
      "0": {
        "order": 0,
        "parts": {
          "0": {
            "position": {
              "x": 0,
              "y": 0,
              "colSpan": 6,
              "rowSpan": 4
            },
            "metadata": {
              "inputs": [],
              "type": "Extension/Microsoft_Azure_Monitoring/Dashboards/Timeline/Widget"
            }
          }
        }
      }
    }
  }
}
```

**Reference**: [Dashboard design best practices](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards-create-programmatically)

### Content and Metrics Selection

**Example Metrics Configuration**:
```json
{
  "metrics": [
    {
      "resourceMetadata": {
        "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}"
      },
      "name": "Percentage CPU",
      "aggregationType": "Average",
      "namespace": "Microsoft.Compute/virtualMachines",
      "metricVisualization": {
        "displayName": "CPU Usage"
      }
    }
  ]
}
```

**Reference**: [Metrics selection guide](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/metrics-charts)

## Integration Components

### API Integration

**Example API Request**:
```powershell
$token = Get-AzAccessToken
$headers = @{
    'Authorization' = "Bearer $($token.Token)"
    'Content-Type' = 'application/json'
}
$uri = "https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Insights/metrics?api-version=2018-01-01"
Invoke-RestMethod -Uri $uri -Headers $headers -Method Get
```

**Reference**: [Azure Monitor REST API](https://learn.microsoft.com/en-us/rest/api/monitor/)

### Webhook Integration

**Example Webhook Configuration**:
```json
{
  "properties": {
    "name": "Sample Webhook",
    "webhookUrl": "https://webhook.example.com/endpoint",
    "headers": {
      "Authorization": "Bearer {token}"
    }
  }
}
```

**Reference**: [Webhook configuration guide](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-common-schema-definitions)

## Additional Resources

### Official Documentation
- [Azure Monitor Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/)
- [Azure Workbooks Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/workbooks-overview)
- [Application Insights Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview)

### Community Resources
- [Azure Monitor Community GitHub](https://github.com/microsoft/AzureMonitorCommunity)
- [Azure Monitor Workbook Templates](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Workbooks)
- [Azure Monitor PowerShell Samples](https://github.com/Azure/azure-powershell/tree/master/src/Monitor)

### Tools and Utilities
- [Azure Monitor PowerShell Module](https://learn.microsoft.com/en-us/powershell/module/az.monitor/)
- [Azure CLI Monitor Extension](https://learn.microsoft.com/en-us/cli/azure/monitor)
- [Azure Monitor REST API Reference](https://learn.microsoft.com/en-us/rest/api/monitor/)

## Next Steps

Ready to learn more about visualizing your monitoring data? Continue to our [Visualization and Dashboards](08-Visualization-Dashboards.md) section.

[Back to Main Document](../README.md) | [Previous: Implementation Guidance](06-Implementation-Guidance.md) | [Next: Visualization and Dashboards](08-Visualization-Dashboards.md) 