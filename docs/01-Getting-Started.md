# Getting Started with AMBA

Welcome to Azure Monitor Baseline Alerts (AMBA). This guide will walk you through the essential steps to begin implementing AMBA in your Azure environment.

## Prerequisites

Before implementing AMBA, ensure your environment meets these requirements:

1. **Azure Subscription Access**
   - Appropriate permissions to create and manage resources
   - Rights to assign and manage Azure policies
   - Access to create and modify Azure Monitor resources

2. **Required Resource Providers**
   - Microsoft.Insights
   - Microsoft.AlertsManagement
   - Microsoft.OperationalInsights

3. **Supporting Azure Services**
   - Azure Monitor enabled
   - Log Analytics workspace
   - Action Groups for notifications (optional)

4. **Command Line Tools**
   - Azure CLI (version 2.40.0 or later)
     - [Install Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
     - Run `az --version` to verify installation
   - Azure PowerShell (version 9.0.0 or later)
     - [Install Azure PowerShell](https://learn.microsoft.com/en-us/powershell/azure/install-azure-powershell)
     - Run `Get-Module -Name Az -ListAvailable` to verify installation

5. **Source Control**
   - Git for cloning the AMBA repository
     - [Install Git](https://git-scm.com/downloads)
     - Run `git --version` to verify installation

## Essential Documentation Links

Before proceeding with deployment, review these essential resources:

1. **AMBA Resources**
   - [AMBA GitHub Repository](https://github.com/Azure/azure-monitor-baseline-alerts)
   - [AMBA Documentation Site](https://azure.github.io/azure-monitor-baseline-alerts/welcome/)
   - [AMBA ALZ Pattern Guide](https://azure.github.io/azure-monitor-baseline-alerts/patterns/alz/)

2. **Azure Monitor Documentation**
   - [Azure Monitor Overview](https://learn.microsoft.com/en-us/azure/azure-monitor/overview)
   - [Alert Management](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview)
   - [Log Analytics Workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview)

3. **Azure Policy Resources**
   - [Azure Policy Overview](https://learn.microsoft.com/en-us/azure/governance/policy/overview)
   - [Policy Assignment Guide](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/assignment-structure)
   - [Policy Parameters](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/definition-structure#parameters)

4. **Deployment Resources**
   - [Resource Provider Registration](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/resource-providers-and-types)
   - [RBAC Documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/overview)
   - [Management Groups](https://learn.microsoft.com/en-us/azure/governance/management-groups/overview)

5. **Best Practices**
   - [Azure Monitor Best Practices](https://learn.microsoft.com/en-us/azure/azure-monitor/best-practices)
   - [Naming Conventions](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)
   - [Resource Tagging](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

## Initial Assessment

Before deployment, conduct these key assessments:

1. **Environment Review**
   - Document existing monitoring solutions
   - Identify critical resources requiring monitoring
   - List current alert configurations

2. **Team Preparation**
   - Identify key stakeholders
   - Define roles and responsibilities
   - Plan training requirements

3. **Resource Requirements**
   - Estimate Log Analytics workspace needs
   - Calculate potential costs
   - Plan resource scaling

4. **ALZ Integration Assessment**
   - Map current management group hierarchy
     - Document existing management group structure
     - Identify landing zones and their purposes ([ALZ Landing Zone Design Guide](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-areas))
     - Review subscription organization ([ALZ Subscription Organization](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/resource-org-subscriptions))
   - Evaluate policy inheritance
     - Document existing policy assignments ([Policy Implementation Guide](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/assignment-structure))
     - Identify potential conflicts with AMBA policies ([AMBA Policy Reference](https://azure.github.io/azure-monitor-baseline-alerts/patterns/alz/initiatives/))
     - Plan policy scope and exclusions ([Policy Scope Guide](https://learn.microsoft.com/en-us/azure/governance/policy/concepts/scope))
   - Assess monitoring requirements by landing zone
     - Determine monitoring needs for each landing zone type ([ALZ Monitoring Guide](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/management-monitoring))
     - Identify zone-specific alert thresholds ([AMBA Alert Customization](https://azure.github.io/azure-monitor-baseline-alerts/patterns/alz/HowTo/customize/))
     - Plan alert routing per landing zone ([Action Groups Guide](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/action-groups))
   - Review organizational alignment
     - Map business units to landing zones ([ALZ Reference Architecture](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/architecture))
     - Document compliance requirements per zone ([Azure Compliance Documentation](https://learn.microsoft.com/en-us/azure/compliance/))
     - Identify cross-zone dependencies ([ALZ Network Topology](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity))

5. **ALZ-AMBA Implementation Planning**
   - Deployment strategy
     - Determine deployment scope ([AMBA Deployment Patterns](https://azure.github.io/azure-monitor-baseline-alerts/patterns/alz/HowTo/deploy/))
     - Plan phased rollout if needed ([ALZ Implementation Guide](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/implementation-guidelines))
     - Identify pilot landing zones ([ALZ Platform Design](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/platform))
   - Permission mapping
     - Review existing RBAC assignments ([ALZ Identity and Access](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/design-area/identity-access))
     - Plan monitoring roles per landing zone ([Azure Monitor RBAC](https://learn.microsoft.com/en-us/azure/azure-monitor/roles-permissions-security))
     - Define alert access requirements ([Alert Role Permissions](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-manage-access))
   - Monitoring customization
     - Document zone-specific monitoring requirements ([AMBA Customization Guide](https://azure.github.io/azure-monitor-baseline-alerts/patterns/alz/HowTo/customize/))
     - Plan custom threshold values ([Metric Alert Configuration](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-metric-overview))
     - Define zone-specific alert suppression rules ([Alert Processing Rules](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-processing-rules))
   - Integration points
     - Map existing tools to ALZ structure ([Azure Monitor Integration](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/integrate-with-other-services))
     - Plan cross-zone monitoring workflows ([Workflow Automation](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-action-rules))
     - Define escalation paths per landing zone ([Alert Escalation Guide](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-escalate-problem))

## Deployment Options

AMBA offers multiple deployment methods to suit your needs. For comprehensive deployment guidance, see our [Deployment Process](04-Deployment-Process.md) documentation, including:
- [Understanding Resource Creation](04-Deployment-Process.md#understanding-resource-creation)
- [Making It Your Own](04-Deployment-Process.md#making-it-your-own)
- [Deployment Journey](04-Deployment-Process.md#deployment-journey)
- [Maintaining Your Deployment](04-Deployment-Process.md#maintaining-your-deployment)

### 1. Azure Portal Deployment
- Best for: Small environments, initial testing
- Features: Visual interface, guided setup
- See: [Portal Deployment in Detail](04-Deployment-Process.md#portal-deployment-recommended-for-small-environments)
- Documentation: 
  - [Portal Deployment Guide](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-create-new-alert-rule)
  - [Azure Monitor Prerequisites](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/resource-manager-samples)
  - [Deployment Best Practices](https://learn.microsoft.com/en-us/azure/azure-monitor/best-practices)

### 2. Infrastructure as Code
- Best for: Enterprise deployments, automation
- Features: Version control, repeatable deployments
- See: [Infrastructure as Code Details](04-Deployment-Process.md#infrastructure-as-code)

```json
{
  "name": "AMBA-Base-Deployment",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[variables('templateUri')]",
      "contentVersion": "1.0.0.0"
    }
  }
}
```

### 3. Azure CLI
- Best for: Scripted deployments, automation
- Features: Cross-platform, scriptable
- See: [Azure CLI Deployment Details](04-Deployment-Process.md#azure-cli-deployment-recommended-for-automation)
- For complete examples, see: [Deployment Process Examples](04-Deployment-Process.md#example-parameter-file-structure)

```bash
# Register required resource providers
az provider register --namespace Microsoft.Insights
az provider register --namespace Microsoft.AlertsManagement
az provider register --namespace Microsoft.OperationalInsights

# Create base resource group
az group create --name amba-monitoring --location eastus
```

### 4. PowerShell
- Best for: Windows environments, advanced automation
- Features: Deep Windows integration, rich scripting
- See: [PowerShell Deployment Details](04-Deployment-Process.md#powershell-deployment-alternative-automation)
- For validation steps, see: [Deployment Validation](04-Deployment-Process.md#phase-3-validation)

```powershell
# Register providers
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
Register-AzResourceProvider -ProviderNamespace Microsoft.AlertsManagement
Register-AzResourceProvider -ProviderNamespace Microsoft.OperationalInsights
```

## Quick Start Guide

1. **Environment Setup**
   ```bash
   # Clone the AMBA repository
   git clone https://github.com/Azure/azure-monitor-baseline-alerts
   cd azure-monitor-baseline-alerts

   # Create resource group
   az group create --name amba-monitoring-rg --location eastus

   # Deploy Log Analytics workspace
   az monitor log-analytics workspace create \
       --resource-group amba-monitoring-rg \
       --workspace-name amba-workspace \
       --location eastus \
       --sku PerGB2018
   ```

2. **Deploy Policy Initiatives**
   ```bash
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
   ```

3. **Policy Assignment**
   ```bash
   # Assign policies at management group level
   az policy assignment create \
       --name "amba-monitoring" \
       --scope "/providers/Microsoft.Management/managementGroups/{your-mg-name}" \
       --policy-set-definition "amba-monitoring-initiative" \
       --params "policies/assignments/amba-monitoring-assignment.json"
   ```

4. **Initial Validation**
   ```bash
   # Verify policy assignments
   az policy assignment list --query "[?contains(displayName, 'AMBA')]"

   # Verify alert rules
   az monitor metrics alert list \
       --resource-group amba-monitoring-rg \
       --output table
   ```

## First Steps After Deployment

1. **Verify Base Configuration**
   - Check resource provider registration
   - Confirm policy assignments
   - Test basic alert flow

2. **Configure Notifications**
   ```json
   {
     "name": "AMBA-Critical-Alerts",
     "type": "Microsoft.Insights/actionGroups",
     "properties": {
       "groupShortName": "CritAlerts",
       "enabled": true,
       "emailReceivers": [
         {
           "name": "Operations Team",
           "emailAddress": "ops@contoso.com"
         }
       ]
     }
   }
   ```

3. **Review Initial Alerts**
   - Access the Azure Portal
   - Navigate to Azure Monitor > Alerts
   - Review deployed alert rules

## Next Steps

Once you've completed the initial setup:

1. Review the [Technical Strategy](02-Technical-Strategy.md) for architectural insights
2. Plan your customization approach
3. Consider integration with existing tools
4. Schedule team training sessions

For detailed architectural information and design principles, proceed to the [Technical Strategy](02-Technical-Strategy.md) section.

[Back to Main Document](../AMBA%20Design%20Document.md) | [Next: Technical Strategy](02-Technical-Strategy.md) 