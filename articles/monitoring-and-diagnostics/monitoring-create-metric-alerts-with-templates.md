---
title: Creare un avviso metrica con un modello di Resource Manager
description: Informazioni su come usare un modello di Resource Manager per creare un avviso di metrica.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/26/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 4dba3d182c7c2927aa4feb88e70fe5711fcc6818
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932212"
---
# <a name="create-a-metric-alert-with-a-resource-manager-template"></a>Creare un avviso metrica con un modello di Resource Manager
Questo articolo illustra come usare un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per configurare gli [avvisi delle metriche più recenti](monitoring-near-real-time-metric-alerts.md) in Monitoraggio di Azure. I modelli di Resource Manager consentono di configurare gli avvisi a livello di codice in modo coerente e riproducibile tra gli ambienti. Gli avvisi delle metriche più recenti sono attualmente disponibili in [questo set di tipi di risorse](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

> [!IMPORTANT]
> Il modello di Resource Manager specificato per l'avviso di metrica non sarà compatibile per il tipo di risorsa: Microsoft.OperationalInsights/workspaces; in quanto supporto per le metriche in Log Analytics è in anteprima. Gli utenti interessati a usare la funzionalità di anteprima con il modello di risorse, possono contattare [Feedback degli avvisi di Azure](mailto:azurealertsfeedback@microsoft.com)


I passaggi di base sono i seguenti:

1. Usare uno dei modelli di seguito come file JSON che descrive come creare l'avviso.
2. Modificare e usare il file dei parametri corrispondente come JSON per personalizzare l'avviso
3. Distribuire il modello usando un [metodo di distribuzione qualsiasi](../azure-resource-manager/resource-group-template-deploy.md).


## <a name="resource-manager-template-for-a-simple-metric-alert"></a>Modello di Resource Manager per un avviso di metrica semplice
Per creare un avviso usando un modello di Resource Manager, creare una risorsa di tipo `Microsoft.Insights/metricAlerts` e completare tutte le proprietà correlate. Di seguito è riportato un modello di esempio che crea una regola di avviso della metrica.

Salvare il file JSON come simplemetricalert.json ai fini di questa procedura dettagliata.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

Una spiegazione dello schema e delle proprietà per una regola di avviso [è disponibile qui](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate).

È possibile impostare i valori per i parametri nella riga di comando o tramite un file di parametri. Di seguito viene fornito un file di parametri di esempio. 

Salvare il codice JSON seguente come simplemetricalert.parameters.json e modificarlo in base alle esigenze.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


È possibile creare l'avviso di metrica usando il modello e il file dei parametri tramite PowerShell o l'interfaccia della riga di comando di Azure.

Uso di Azure PowerShell

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile simplemetricalert.json -TemplateParametersFile simplemetricalert.parameters.json
```


Utilizzare l'interfaccia della riga di comando di Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file simplemetricalert.json \
    --parameters @simplemetricalert.parameters.json
```

> [!NOTE]
>
> Anche se è possibile che l'avviso di metrica sia stato creato in un gruppo di risorse diverso rispetto alla risorsa di destinazione, è consigliabile usare lo stesso gruppo di risorse della risorsa di destinazione.

## <a name="resource-manager-template-for-a-more-advanced-metric-alert"></a>Modello di Resource Manager per un avviso di metrica più avanzato
Gli avvisi delle metriche più recenti supportano la creazione di avvisi in base a metriche multidimensionali e la specifica di più criteri. È possibile usare il modello seguente per creare un avviso di metrica più avanzato in base a metriche dimensionali e specificare più criteri.

Salvare il file JSON di seguito come advancedmetricalert.json ai fini di questa procedura dettagliata.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}
```

È possibile usare il modello riportato in precedenza con il file dei parametri fornito di seguito. 

Salvare e modificare il file JSON di seguito come advancedmetricalert.parameters.json ai fini di questa procedura dettagliata.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Includes",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Includes",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Includes",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }      
        },          
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/Default-ActivityLogAlerts/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }    
}
```


È possibile creare l'avviso di metrica usando il modello e il file dei parametri tramite PowerShell o l'interfaccia della riga di comando di Azure dalla directory di lavoro corrente.

Uso di Azure PowerShell
```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile advancedmetricalert.json -TemplateParametersFile advancedmetricalert.parameters.json
```



Utilizzare l'interfaccia della riga di comando di Azure
```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file advancedmetricalert.json \
    --parameters @advancedmetricalert.parameters.json
```

>[!NOTE]
>
> Anche se è possibile che l'avviso di metrica sia stato creato in un gruppo di risorse diverso rispetto alla risorsa di destinazione, è consigliabile usare lo stesso gruppo di risorse della risorsa di destinazione.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sugli [avvisi in Azure](monitoring-overview-unified-alerts.md)
* Informazioni su come [creare un gruppo di azione con i modelli di Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)
