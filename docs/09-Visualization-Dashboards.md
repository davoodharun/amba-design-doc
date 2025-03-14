# Visualization and Dashboards

AMBA's visualization and dashboard capabilities provide powerful tools for understanding and managing your Azure environment's health and performance. This section explores how to create effective dashboards and visualizations that deliver actionable insights. For more information, see [Azure Monitor visualization overview](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/visualization-overview).

## Azure Workbooks

Azure Workbooks serve as a central platform for creating interactive and dynamic dashboards. For detailed information, see [Azure Workbooks documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/workbooks-overview).

### Alert Management Workbook

The Alert Management Workbook is designed to provide a comprehensive overview of your alert status. This workbook includes sections for active alerts, alert trends, and resolution times, helping you quickly identify and address issues.

The workbook's interactive elements allow you to drill down into specific alerts, analyze patterns, and take action based on the insights provided. This enhances your ability to manage and respond to alerts effectively.

**Example Alert Management Template**:
```json
{
  "version": "Notebook/1.0",
  "items": [
    {
      "type": "query",
      "name": "Alert Summary",
      "query": "AlertsManagementResources\n| where type == 'microsoft.alertsmanagement/alerts'\n| where properties.essentials.startDateTime > ago(24h)\n| summarize Count=count() by tostring(properties.essentials.severity)",
      "timeContext": {
        "durationMs": 86400000
      },
      "queryType": 1,
      "visualization": "piechart",
      "chartSettings": {
        "seriesLabelSettings": [
          {
            "seriesName": "Sev0",
            "color": "red"
          },
          {
            "seriesName": "Sev1",
            "color": "orange"
          }
        ]
      }
    }
  ]
}
```

**Reference**: [Alert Management Workbook samples](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Workbooks/AlertManagement)

### Resource Health Workbook

The Resource Health Workbook focuses on the health status of your Azure resources. It provides detailed information about resource availability, performance metrics, and any issues that may affect resource health.

This workbook helps you maintain a clear view of your resource health, enabling you to proactively address potential issues before they impact your operations.

**Example Resource Health Query**:
```kusto
ResourceHealth
| where TimeGenerated > ago(24h)
| extend ResourceType = tostring(split(ResourceId, '/')[6])
| summarize LastStatus = arg_max(TimeGenerated, *) by ResourceId
| project ResourceType, Status = tostring(properties.currentHealthStatus)
| summarize Count=count() by ResourceType, Status
```

**Reference**: [Resource Health monitoring](https://learn.microsoft.com/en-us/azure/service-health/resource-health-overview)

### Performance Monitoring Workbook

The Performance Monitoring Workbook offers insights into the performance of your Azure resources. It includes metrics for CPU utilization, memory usage, and other performance indicators, helping you optimize resource usage and maintain efficient operations.

The workbook's visualizations and interactive elements allow you to analyze performance trends and identify areas for improvement.

**Example Performance Dashboard**:
```json
{
  "type": "query",
  "name": "Resource Performance",
  "query": "Perf\n| where ObjectName == 'Processor' and CounterName == '% Processor Time'\n| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer\n| render timechart",
  "timeContext": {
    "durationMs": 14400000
  },
  "visualization": "linechart",
  "chartSettings": {
    "ySettings": {
      "min": 0,
      "max": 100
    }
  }
}
```

**Reference**: [Performance monitoring guide](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/monitor-performance)

## Azure Managed Grafana Integration

Azure Managed Grafana provides a powerful platform for creating custom dashboards. For detailed information, see [Azure Managed Grafana documentation](https://learn.microsoft.com/en-us/azure/managed-grafana/overview).

### Infrastructure Monitoring Dashboard

The Infrastructure Monitoring Dashboard focuses on monitoring the health and performance of your infrastructure components. This dashboard includes metrics for CPU and memory utilization, network performance, and storage capacity, helping you maintain a robust infrastructure.

**Example Grafana Dashboard JSON**:
```json
{
  "dashboard": {
    "id": null,
    "title": "Infrastructure Overview",
    "tags": ["azure", "infrastructure"],
    "timezone": "browser",
    "panels": [
      {
        "title": "CPU Usage",
        "type": "graph",
        "datasource": "Azure Monitor",
        "targets": [
          {
            "queryType": "Azure Monitor",
            "subscription": "${subscription}",
            "metricDefinition": "Microsoft.Compute/virtualMachines",
            "metricName": "Percentage CPU"
          }
        ]
      }
    ]
  }
}
```

**Reference**: [Grafana Azure Monitor integration](https://learn.microsoft.com/en-us/azure/managed-grafana/how-to-use-azure-monitor-data-source)

### Custom Dashboard Creation

Creating custom dashboards in Azure Managed Grafana involves selecting the most relevant metrics and visualizations for your needs. This includes configuring data sources, creating panels, and setting up alerts to ensure you have a comprehensive view of your environment.

**Example Grafana Panel Configuration**:
```json
{
  "panel": {
    "datasource": "Azure Monitor",
    "fieldConfig": {
      "defaults": {
        "color": {
          "mode": "palette-classic"
        },
        "thresholds": {
          "mode": "absolute",
          "steps": [
            { "color": "green", "value": null },
            { "color": "red", "value": 80 }
          ]
        }
      }
    }
  }
}
```

**Reference**: [Grafana dashboard best practices](https://grafana.com/docs/grafana/latest/best-practices/best-practices-for-creating-dashboards/)

## Dashboard Templates

Dashboard templates provide a starting point for creating effective dashboards. AMBA offers pre-built templates that you can customize to meet your specific needs.

### Alert Overview Template

The Alert Overview Template is designed to provide a clear view of your alert status. This template includes sections for active alerts, alert trends, and resolution times, helping you manage and respond to alerts effectively.

**Example Alert Overview Configuration**:
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
              "inputs": [
                {
                  "name": "alertRules",
                  "value": {
                    "scope": ["${subscription}"],
                    "severity": ["Sev0", "Sev1"]
                  }
                }
              ],
              "type": "Extension/Microsoft_Azure_Monitoring/Dashboards/AlertsGrid"
            }
          }
        }
      }
    }
  }
}
```

**Reference**: [Azure portal dashboard templates](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards-create-programmatically)

### Performance Dashboard Template

The Performance Dashboard Template focuses on visualizing performance metrics across your Azure environment. This template includes panels for CPU utilization, memory usage, and network performance, helping you optimize resource usage.

**Example Performance Metrics Configuration**:
```json
{
  "metrics": [
    {
      "timeGrain": "PT5M",
      "metricAggregation": "Average",
      "resourceMetadata": {
        "id": "${resourceId}"
      },
      "name": "Percentage CPU",
      "visualization": {
        "chartType": "Line",
        "legendVisualization": {
          "isVisible": true,
          "position": "Bottom"
        }
      }
    }
  ]
}
```

**Reference**: [Azure Monitor metrics visualization](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/metrics-charts)

## Visualization Best Practices

Creating effective visualizations involves following best practices that enhance clarity and usability. This section explores key principles for designing dashboards and visualizations.

### Design Principles

Design principles guide the creation of effective dashboards. Consider organizing information logically, creating clear visual hierarchies, and adding interactive elements to enhance usability. This includes using appropriate chart types, color coding, and layout options to present data effectively.

**Example Color Scheme Configuration**:
```json
{
  "visualization": {
    "palette": {
      "name": "Azure",
      "colors": [
        "#0072C6",
        "#00BCF2",
        "#7FBA00",
        "#F25022"
      ]
    },
    "thresholds": [
      {
        "value": 90,
        "color": "#F25022"
      },
      {
        "value": 70,
        "color": "#FFB900"
      }
    ]
  }
}
```

**Reference**: [Data visualization best practices](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards-create-programmatically#visualization-best-practices)

### Data Presentation

Data presentation involves choosing the most relevant data to display and presenting it in a clear and actionable manner. This includes selecting key performance indicators, configuring alert displays, and including business impact metrics.

**Example KQL Query for Data Aggregation**:
```kusto
Perf
| where TimeGenerated > ago(1h)
| where ObjectName == "Processor"
| summarize AvgCPU = avg(CounterValue) by bin(TimeGenerated, 5m), Computer
| render timechart
```

**Reference**: [KQL query best practices](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/best-practices)

## Custom Visualizations

Custom visualizations allow you to create unique and effective ways to present your monitoring data. This section explores how to create custom charts and visualizations that provide insights into your environment.

### KQL Queries

KQL queries enable you to extract and analyze data from your Azure environment. This includes creating custom metrics, filtered views, and aggregations that provide insights into your monitoring data.

**Example Advanced KQL Query**:
```kusto
let timeRange = 1h;
let interval = 5m;
Perf
| where TimeGenerated > ago(timeRange)
| where ObjectName == "Processor"
| summarize 
    AvgCPU = avg(CounterValue),
    MaxCPU = max(CounterValue),
    P95CPU = percentile(CounterValue, 95)
    by bin(TimeGenerated, interval), Computer
| project
    TimeGenerated,
    Computer,
    AvgCPU = round(AvgCPU, 2),
    MaxCPU = round(MaxCPU, 2),
    P95CPU = round(P95CPU, 2)
```

**Reference**: [KQL query language reference](https://learn.microsoft.com/en-us/azure/data-explorer/kusto/query/)

### Metric Visualizations

Metric visualizations involve creating charts and graphs that represent your monitoring data effectively. This includes selecting appropriate chart types, configuring data sources, and setting up interactive elements to enhance usability.

**Example Custom Metric Configuration**:
```json
{
  "metricVisualizations": [
    {
      "resourceMetadata": {
        "id": "${resourceId}"
      },
      "name": "Custom CPU Metric",
      "aggregationType": "Average",
      "visualization": {
        "displayName": "CPU Usage",
        "chartType": "Line",
        "color": "#0072C6"
      }
    }
  ]
}
```

**Reference**: [Custom metrics documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/metrics-custom-overview)

## Dashboard Management

Managing your dashboards involves regular updates and maintenance to ensure they continue to provide valuable insights. This section explores key aspects of dashboard management.

### Access Control

Access control ensures that your dashboards are accessible to the right users. This includes configuring permissions, managing user roles, and ensuring that sensitive data is protected.

**Example RBAC Configuration**:
```json
{
  "properties": {
    "roleName": "Dashboard Viewer",
    "description": "Can view dashboards",
    "assignableScopes": [
      "/subscriptions/${subscriptionId}"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Portal/dashboards/read"
        ],
        "notActions": []
      }
    ]
  }
}
```

**Reference**: [Dashboard access control](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboard-share-access)

### Version Control

Version control helps you manage changes to your dashboards effectively. This includes tracking updates, managing configurations, and ensuring that your dashboards remain consistent with your monitoring strategy.

**Example Dashboard Version Control**:
```powershell
# Export dashboard
$dashboard = Get-AzPortalDashboard -ResourceGroupName "MyRG" -DashboardName "MyDashboard"
$dashboard | ConvertTo-Json -Depth 100 | Out-File "dashboard_backup.json"

# Import dashboard
$dashboardJson = Get-Content "dashboard_backup.json" | ConvertFrom-Json
New-AzPortalDashboard -ResourceGroupName "MyRG" -DashboardName "MyDashboard" -DashboardJson $dashboardJson
```

**Reference**: [Dashboard management with PowerShell](https://learn.microsoft.com/en-us/powershell/module/az.portal/new-azportaldashboard)

## Integration Options

Integration options enable AMBA to work seamlessly with various monitoring tools and services. This section explores how to configure these components to enhance your monitoring capabilities.

### Power BI Integration

Power BI integration allows you to leverage Power BI's capabilities to create detailed and interactive dashboards. This includes configuring data sources, creating reports, and setting up alerts to ensure you have a comprehensive view of your environment.

**Example Power BI Dataset Configuration**:
```json
{
  "name": "Azure Monitor Dataset",
  "defaultMode": "Push",
  "tables": [
    {
      "name": "Metrics",
      "columns": [
        {
          "name": "TimeGenerated",
          "dataType": "DateTime"
        },
        {
          "name": "MetricName",
          "dataType": "String"
        },
        {
          "name": "MetricValue",
          "dataType": "Double"
        }
      ]
    }
  ]
}
```

**Reference**: [Power BI integration guide](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/powerbi)

### Custom Portal Integration

Custom portal integration enables you to create a personalized monitoring experience. This includes configuring portals, managing access, and ensuring that your monitoring data is presented effectively.

**Example Portal Extension**:
```typescript
export class MonitoringDashboard {
  public static create(portalContext: PortalContext): void {
    const dashboardPart = new DashboardPart();
    dashboardPart.addTile(new MetricsTile({
      title: "CPU Usage",
      query: "Perf | where ObjectName == 'Processor'",
      visualization: "LineChart"
    }));
  }
}
```

**Reference**: [Azure portal extension development](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-extension-development-overview)

## Additional Resources

### Official Documentation
- [Azure Monitor Visualization Guide](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/visualization-overview)
- [Azure Workbooks Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/workbooks-overview)
- [Azure Managed Grafana Documentation](https://learn.microsoft.com/en-us/azure/managed-grafana/)

### Community Resources
- [Azure Monitor Community GitHub](https://github.com/microsoft/AzureMonitorCommunity)
- [Grafana Azure Monitor Plugin](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource/)
- [Azure Monitor Workbook Templates](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Workbooks)

### Tools and Utilities
- [Azure Monitor PowerShell Module](https://learn.microsoft.com/en-us/powershell/module/az.monitor/)
- [Azure CLI Monitor Extension](https://learn.microsoft.com/en-us/cli/azure/monitor)
- [Azure Monitor REST API Reference](https://learn.microsoft.com/en-us/rest/api/monitor/)

## Next Steps

Ready to learn more about the next steps in your AMBA implementation? Continue to our [Next Steps](10-Next-Steps.md) section.

[Back to Main Document](../README.md) | [Previous: Monitoring Tools Integration](07-Monitoring-Tools.md) | [Next: Next Steps](10-Next-Steps.md) 