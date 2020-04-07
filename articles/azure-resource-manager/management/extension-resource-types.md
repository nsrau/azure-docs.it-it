---
title: Tipi di risorsa dell'estensione
description: Gli elenchi dei tipi di risorse di Azure vengono usati per estendere le funzionalità di altri tipi di risorse.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754865"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipi di risorse che estendono le funzionalità di altre risorse

Una risorsa di estensione è una risorsa che aggiunge funzionalità a un'altra risorsa. Ad esempio, il blocco delle risorse è una risorsa di estensione. Si applica un blocco di risorsa a un'altra risorsa per impedire che venga eliminato o modificato. Non ha senso creare un blocco di risorse da solo. Una risorsa di estensione viene sempre applicata a un'altra risorsa.

## <a name="extension-resource-types"></a>Tipi di risorsa dell'estensione

- Microsoft.Advisor/configurations
- Microsoft.Advisor/raccomandazioni
- Microsoft.Advisor/soppressioni
- Microsoft.AlertsGestione/Avvisi
- Microsoft.AlertsManagement/alertsRiepilogo
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/permissions
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinitions
- Microsoft.Billing/BillingPeriods (Periodi di fatturazione/fatturazione)
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints
- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumo/Saldi
- Microsoft.Consumo/Budget
- Microsoft.Consumo/Spese
- Microsoft.Consumption/CostTags
- Microsoft.Consumo/Previsioni
- Microsoft.Consumo/Marketplace
- Microsoft.Consumption/OperationResults
- Microsoft.Consumption/OperationStatus (StatoOperazione)Microsoft.Consumption/OperationStatus (Statooperazione/
- Microsoft.Consumo/Schede prezzi
- Microsoft.Consumo/ReservationDetails
- Microsoft.Consumo/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumo/ReservationTransactions
- Microsoft.Consumo/Tag
- Microsoft.Consumo/Termini
- Microsoft.Consumption/UsageDetails (Informazioni in lingua inglese)
- Microsoft.Consumo/crediti
- Microsoft.Consumo/eventi
- Microsoft.Consumo/lotti
- Microsoft.Consumo/prodotti
- Microsoft.Consumo/tenant
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Avvisi
- Microsoft.CostManagement/Budget
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/Views
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings (informazioni in lingua inglese)
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/tipi di evento
- microsoft.insights/extendedDiagnosticSettings (informazioni in lingua inglese)
- Microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions (informazioni in inglese)
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transazioni
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesConfiguration/sourceControlConfigurations
- Microsoft.Maintenance/applyAggiornamenti
- Microsoft.Maintenance/configurationAssegnazioni
- Microsoft.Maintenance/updates (Manutenzione/aggiornamenti)
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementassociazioni
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults    
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/eventi
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/notifiche
- Microsoft.Resources/collegamenti
- Microsoft.Resources/tag
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/valutazioni
- Microsoft.Security/complianceResults (Protezione/conformità)
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/aggregazioni
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/segnalibri
- Microsoft.SecurityInsights/casi
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/entità
- Microsoft.SecurityInsights/entityQueries
- Microsoft.SecurityInsights/incidents
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/impostazioni
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscription/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/components
- Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitor
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passaggi successivi

- Per ottenere l'ID risorsa per una risorsa di estensione in un modello di Azure Resource Manager, usare [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Per un esempio di creazione di una risorsa di estensione in un modello, vedere Sottoscrizioni di [eventi Griglia di eventi](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
