---
title: Tipi di risorsa dell'estensione
description: Elenca i tipi di risorse di Azure usati per estendere le funzionalità di altri tipi di risorse.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658452"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipi di risorse che estendono le funzionalità di altre risorse

Una risorsa di estensione è una risorsa che aggiunge le funzionalità di un'altra risorsa. Ad esempio, il blocco di risorsa è una risorsa di estensione. Applicare un blocco di risorsa a un'altra risorsa per impedirne l'eliminazione o la modifica. Non ha senso creare un blocco di risorsa da solo. Una risorsa di estensione viene sempre applicata a un'altra risorsa.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft. Advisor/configurazioni
- Microsoft. Advisor/raccomandazioni
- Microsoft. Advisor/evitamenti

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft. AlertsManagement/Alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft. automanage

- Microsoft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft. Billing/billingPeriods
- Microsoft. Billing/billingPermissions
- Microsoft. Billing/billingRoleAssignments
- Microsoft. Billing/billingRoleDefinitions
- Microsoft. Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/Blueprints

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft. consumer/AggregatedCost
- Microsoft. consumo/Saldi
- Microsoft. consumo/budget
- Microsoft. consumo/addebiti
- Microsoft. consumer/CostTags
- Microsoft. consumo/crediti
- Microsoft. consumo/eventi
- Microsoft. consumo/previsioni
- Microsoft. consumo/molti
- Microsoft. consumer/Marketplace
- Microsoft. consumer/Pricesheets
- Microsoft. consumo/prodotti
- Microsoft. consumer/ReservationDetails
- Microsoft. consumer/ReservationRecommendationDetails
- Microsoft. consumer/ReservationRecommendations
- Microsoft. consumer/ReservationSummaries
- Microsoft. consumer/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/budgets
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/Exports
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/previsione
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- Microsoft. Insights/Baseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/EventTypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/log
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metrica
- Microsoft. Insights/cartelle di lavoro
- Microsoft. Insights/topologia
- Microsoft. Insights/transazioni

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft. ManagedIdentity/identità

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft. PolicyInsights/attestazioni
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/notifiche

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft. resources/Links
- Microsoft. resources/Tag

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessment
- Microsoft. Security/compliances
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/Devices
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft. SecurityInsights/Aggregations
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/segnalibri
- Microsoft. SecurityInsights/case
- Microsoft. SecurityInsights/dataconnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/Entities
- Microsoft. SecurityInsights/eventi imprevisti
- Microsoft. SecurityInsights/Settings
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft. SerialConsole. PPE

- Microsoft. SerialConsole. PPE/serialPort

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>microsoft.support

- Microsoft. support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/monitoraggi
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passaggi successivi

- Per ottenere l'ID risorsa per una risorsa di estensione in un modello di Azure Resource Manager, usare [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Per un esempio di creazione di una risorsa di estensione in un modello, vedere [sottoscrizioni di eventi di griglia di eventi](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
