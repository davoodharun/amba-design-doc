# Deployment Process

The deployment of AMBA in your Azure environment is a structured process that ensures consistent and effective monitoring coverage. This section guides you through the deployment journey, from initial preparation to final validation and cleanup. For a comprehensive overview of Azure Monitor deployment concepts, see the [Azure Monitor deployment best practices](https://learn.microsoft.com/en-us/azure/azure-monitor/best-practices-plan).

## Getting Started

Before beginning the deployment process, it's essential to understand your environment's current state and requirements. This understanding forms the foundation for a successful AMBA implementation. Start by assessing your existing monitoring setup, identifying gaps in coverage, and documenting your monitoring requirements. For guidance on monitoring strategy, refer to [Azure Monitor planning documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/best-practices-plan).

### Environment Assessment Example

Here's an example assessment checklist:

```plaintext
1. Resource Inventory
   □ List all Azure subscriptions
   □ Document resource groups and their purposes
   □ Identify critical resources requiring monitoring
   □ Map dependencies between resources

2. Current Monitoring Status
   □ Document existing alert rules
   □ List active Log Analytics workspaces
   □ Review current monitoring tools
   □ Identify monitoring gaps

3. Integration Requirements
   □ List external monitoring tools
   □ Document notification channels
   □ Identify automation requirements
   □ Map team responsibilities
```

## AMBA Repository

The Azure Monitor Baseline Alerts (AMBA) repository is hosted on GitHub at [https://github.com/Azure/azure-monitor-baseline-alerts](https://github.com/Azure/azure-monitor-baseline-alerts). This repository contains all the necessary policy definitions, parameters, and deployment templates needed for implementing AMBA in your environment. Key resources in the repository include:

- Policy definitions and initiatives
- Parameter templates and examples
- Deployment scripts and guidance
- Best practices documentation
- Community contributions and feedback

Before proceeding with deployment, clone the repository to access the required files:

```bash
git clone https://github.com/Azure/azure-monitor-baseline-alerts
cd azure-monitor-baseline-alerts
```

## Essential Prerequisites

Before deploying AMBA, ensure your environment meets the necessary prerequisites. For a complete list of prerequisites, see [Azure Monitor prerequisites documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/monitor-requirements-overview).

### Resource Provider Registration

Register these required resource providers using Azure CLI. For more information about resource providers, see [Azure resource providers documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/resource-providers-and-types).

```bash
# Register required resource providers
az provider register --namespace Microsoft.Insights
az provider register --namespace Microsoft.AlertsManagement
az provider register --namespace Microsoft.OperationalInsights

# Verify registration status
az provider show -n Microsoft.Insights -o table
az provider show -n Microsoft.AlertsManagement -o table
az provider show -n Microsoft.OperationalInsights -o table
```

### Permission Requirements

Ensure you have these minimum permissions. For detailed RBAC information, see [Azure RBAC documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview):

Example role assignment using Azure CLI:

```bash
# Assign Contributor role at subscription level
az role assignment create --assignee "user@example.com" \
    --role "Contributor" \
    --scope "/subscriptions/{subscription-id}"
```

## Choosing Your Deployment Path

AMBA supports multiple deployment methods. For detailed deployment options, see [Azure deployment options documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deployment-modes).

### Portal Deployment (Recommended for Small Environments)
```plaintext
Advantages:
- Visual interface
- Immediate feedback
- Step-by-step guidance

Best for:
- Initial deployments
- Small environments
- Learning AMBA
```

### Azure CLI Deployment (Recommended for Automation)

The following example shows how to deploy AMBA using Azure CLI. The required files are located in the `policies` directory of the AMBA repository (https://github.com/Azure/azure-monitor-baseline-alerts):

```bash
# Set variables
resourceGroup="amba-monitoring-rg"
location="eastus"
workspaceName="amba-workspace"

# Clone the AMBA repository
git clone https://github.com/Azure/azure-monitor-baseline-alerts
cd azure-monitor-baseline-alerts

# Create resource group
az group create --name $resourceGroup --location $location

# Deploy Log Analytics workspace
az monitor log-analytics workspace create \
    --resource-group $resourceGroup \
    --workspace-name $workspaceName \
    --location $location \
    --sku PerGB2018

# Deploy core monitoring policy initiatives
az policy set-definition create \
    --name "amba-monitoring-initiative" \
    --definitions "policies/initiatives/amba-monitoring.json" \
    --params "policies/parameters/amba-monitoring-params.json"

# Deploy service health initiative
az policy set-definition create \
    --name "amba-service-health" \
    --definitions "policies/initiatives/service-health.json" \
    --params "policies/parameters/service-health-params.json"

# Assign policies at management group level
az policy assignment create \
    --name "amba-monitoring" \
    --scope "/providers/Microsoft.Management/managementGroups/{your-mg-name}" \
    --policy-set-definition "amba-monitoring-initiative" \
    --params "policies/assignments/amba-monitoring-assignment.json"
```

Key files used in deployment:

1. Policy Initiatives:
   - `policies/initiatives/amba-monitoring.json`: Core monitoring policies
   - `policies/initiatives/service-health.json`: Service health monitoring policies
   - `policies/initiatives/platform-monitoring.json`: Platform-specific monitoring

2. Parameter Files:
   - `policies/parameters/amba-monitoring-params.json`: Default monitoring thresholds
   - `policies/parameters/service-health-params.json`: Service health settings
   - `policies/parameters/environment-specific-params.json`: Environment-based configurations

3. Assignment Templates:
   - `policies/assignments/amba-monitoring-assignment.json`: Base monitoring assignment
   - `policies/assignments/service-health-assignment.json`: Service health assignment

### PowerShell Deployment (Alternative Automation)

Similar deployment using PowerShell:

```powershell
# Set variables
$resourceGroup = "amba-monitoring-rg"
$location = "eastus"
$workspaceName = "amba-workspace"
$repoPath = ".\azure-monitor-baseline-alerts"

# Clone the AMBA repository
git clone https://github.com/Azure/azure-monitor-baseline-alerts
cd $repoPath

# Create resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Deploy Log Analytics workspace
New-AzOperationalInsightsWorkspace `
    -ResourceGroupName $resourceGroup `
    -Name $workspaceName `
    -Location $location `
    -Sku PerGB2018

# Deploy monitoring initiatives
$monitoringInitiative = Get-Content ".\policies\initiatives\amba-monitoring.json" | ConvertFrom-Json
$monitoringParams = Get-Content ".\policies\parameters\amba-monitoring-params.json" | ConvertFrom-Json

New-AzPolicySetDefinition `
    -Name "amba-monitoring-initiative" `
    -PolicyDefinition $monitoringInitiative `
    -Parameter $monitoringParams

# Assign policies
$assignmentParams = Get-Content ".\policies\assignments\amba-monitoring-assignment.json" | ConvertFrom-Json

New-AzPolicyAssignment `
    -Name "amba-monitoring" `
    -Scope "/providers/Microsoft.Management/managementGroups/{your-mg-name}" `
    -PolicySetDefinition "amba-monitoring-initiative" `
    -PolicyParameter $assignmentParams
```

### Example Parameter File Structure

Here's an example of the monitoring parameters file (`amba-monitoring-params.json`):

```json
{
  "parameters": {
    "workspaceId": {
      "type": "String",
      "metadata": {
        "displayName": "Log Analytics Workspace",
        "description": "Select Log Analytics workspace from dropdown list"
      }
    },
    "actionGroupId": {
      "type": "String",
      "metadata": {
        "displayName": "Action Group",
        "description": "Select Action Group from dropdown list"
      }
    },
    "alertSeverity": {
      "type": "String",
      "allowedValues": [
        "0",
        "1",
        "2",
        "3",
        "4"
      ],
      "defaultValue": "2",
      "metadata": {
        "displayName": "Severity",
        "description": "Severity of the alert"
      }
    },
    "monitorDisable": {
      "type": "Object",
      "defaultValue": {
        "MonitorDisable": "true"
      },
      "metadata": {
        "displayName": "Monitor Disable Tag",
        "description": "Tag name and value to disable monitoring"
      }
    }
  }
}
```

## Understanding Resource Creation

During deployment, AMBA creates these key resources. For detailed resource specifications, see [Azure resource types documentation](https://learn.microsoft.com/en-us/azure/templates/microsoft.resources/resourcegroups).

### Log Analytics Workspace
For workspace configuration details, see [Log Analytics workspace documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview).

Example workspace configuration:
```json
{
    "properties": {
        "retentionInDays": 30,
        "features": {
            "searchVersion": 1
        },
        "sku": {
            "name": "PerGB2018"
        }
    }
}
```

### Alert Rules
For alert rule configuration details, see [Azure Monitor alerts documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview).

Example alert rule configuration:
```json
{
    "properties": {
        "severity": 2,
        "enabled": true,
        "evaluationFrequency": "PT5M",
        "windowSize": "PT15M",
        "criteria": {
            "allOf": [
                {
                    "threshold": 90,
                    "name": "Metric1",
                    "metricNamespace": "Microsoft.Compute/virtualMachines",
                    "metricName": "Percentage CPU",
                    "operator": "GreaterThan",
                    "timeAggregation": "Average",
                    "criterionType": "StaticThresholdCriterion"
                }
            ]
        }
    }
}
```

## Making It Your Own

### Alert Threshold Customization
For detailed information about alert customization, see [Azure Monitor alert customization documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-metric-overview).

Example of customizing thresholds using tags:

```plaintext
Resource Tag Format:
Key: _amba-{metricName}-threshold-override_
Value: {threshold_value}

Example:
Key: _amba-UtilizationPercentage-threshold-override_
Value: 95
```

### Environment-Specific Settings
For environment configuration best practices, see [Azure environment configuration documentation](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/management).

Example of environment-specific configurations:

```json
{
    "production": {
        "cpuThreshold": 80,
        "memoryThreshold": 85,
        "retentionDays": 90
    },
    "development": {
        "cpuThreshold": 90,
        "memoryThreshold": 90,
        "retentionDays": 30
    }
}
```

## Deployment Journey

### Phase 1: Preparation
For deployment planning guidance, see [Azure deployment planning documentation](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/management).

Example preparation checklist:
```plaintext
□ Environment Assessment Complete
□ Prerequisites Verified
□ Permissions Configured
□ Resource Providers Registered
□ Deployment Method Selected
□ Configuration Parameters Documented
```

### Phase 2: Execution
For deployment execution best practices, see [Azure deployment execution documentation](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/implementation-guidelines).

Example deployment sequence:
```plaintext
1. Create Resource Group
2. Deploy Log Analytics Workspace
3. Configure Data Collection
4. Deploy Policy Initiatives
5. Configure Alert Rules
6. Set Up Action Groups
7. Configure Workbooks
```

### Phase 3: Validation
For deployment validation guidance, see [Azure deployment validation documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deployment-history).

Example validation tests:
```plaintext
□ Verify Resource Creation
□ Test Alert Triggers
□ Confirm Notification Delivery
□ Check Dashboard Access
□ Validate Data Collection
□ Test Performance Impact
```

## Maintaining Your Deployment

### Regular Maintenance Tasks
For maintenance best practices, see [Azure maintenance documentation](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/operate/azure-server-management/server-maintenance).

Example maintenance schedule:
```plaintext
Daily:
- Review active alerts
- Check notification delivery
- Monitor resource usage

Weekly:
- Review alert patterns
- Update thresholds if needed
- Check policy compliance

Monthly:
- Full system review
- Performance optimization
- Documentation updates
```

## Deployment Cleanup

For detailed cleanup procedures, see [Azure resource cleanup documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/delete-resource-group).

### Identifying Resources to Clean
Before cleanup, identify AMBA-related resources:
```bash
# List AMBA resource groups
az group list --query "[?tags.service=='amba']"

# List AMBA policies
az policy assignment list --query "[?contains(displayName, 'AMBA')]"
```

### Cleanup Process
Follow this sequence for safe cleanup:

1. Disable Monitoring:
```bash
# Disable alert rules
az monitor metrics alert update \
    --resource-group {resourceGroup} \
    --name {alertName} \
    --enabled false
```

2. Remove Policy Assignments:
```bash
# Remove policy assignments
az policy assignment delete \
    --name "amba-monitoring-policy" \
    --scope "/subscriptions/{subscription-id}"
```

3. Delete Resources:
```bash
# Delete action groups
az monitor action-group delete \
    --resource-group {resourceGroup} \
    --name {actionGroupName}

# Delete alert rules
az monitor metrics alert delete \
    --resource-group {resourceGroup} \
    --name {alertName}

# Delete Log Analytics workspace
az monitor log-analytics workspace delete \
    --resource-group {resourceGroup} \
    --workspace-name {workspaceName}
```

4. Cleanup Verification:
```bash
# Verify resource removal
az resource list \
    --tag service=amba \
    --output table
```

### Post-Cleanup Tasks
```plaintext
□ Verify all AMBA resources are removed
□ Document removed configurations
□ Update team documentation
□ Archive monitoring data if needed
□ Review dependent systems
```

## Next Steps

Ready to implement AMBA in your environment? Continue to our [Policy Initiatives](05-Policy-Initiatives.md) section to learn about the monitoring policies that AMBA provides.

## References and Additional Resources

### Official Documentation
- [Azure Monitor Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/)
- [Azure Policy Documentation](https://learn.microsoft.com/en-us/azure/governance/policy/)
- [Log Analytics Workspace Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview)
- [Azure CLI Reference](https://learn.microsoft.com/en-us/cli/azure/)
- [Azure PowerShell Reference](https://learn.microsoft.com/en-us/powershell/azure/)

### Best Practices and Guidelines
- [Azure Monitor Best Practices](https://learn.microsoft.com/en-us/azure/azure-monitor/best-practices-plan)
- [Azure Resource Naming Conventions](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)
- [Azure Tagging Strategies](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)
- [Azure RBAC Best Practices](https://learn.microsoft.com/en-us/azure/role-based-access-control/best-practices)

### Tools and Utilities
- [Azure Resource Explorer](https://resources.azure.com/)
- [Azure Monitor GitHub Repository](https://github.com/microsoft/AzureMonitorCommunity)
- [Azure Policy Samples](https://github.com/Azure/azure-policy)
- [Azure QuickStart Templates](https://github.com/Azure/azure-quickstart-templates)

### Related Services
- [Azure Resource Manager Documentation](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview)
- [Azure Action Groups Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/action-groups)
- [Azure Workbooks Documentation](https://learn.microsoft.com/en-us/azure/azure-monitor/visualize/workbooks-overview)

### Community Resources
- [Azure Monitor Community on GitHub](https://github.com/microsoft/AzureMonitorCommunity)
- [Azure Monitor Blog](https://techcommunity.microsoft.com/t5/azure-monitor-blog/bg-p/AzureMonitorBlog)
- [Azure Monitor Tech Community](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)

[Back to Main Document](../README.md) | [Previous: ALZ Integration](03-ALZ-Integration.md) | [Next: Policy Initiatives](05-Policy-Initiatives.md) 