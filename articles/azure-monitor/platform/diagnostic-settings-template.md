---
title: Creare un'impostazione di diagnostica in Azure usando un modello di Resource Manager
description: Creare impostazioni di diagnostica usando un modello di Resource Manager per inoltrare i log della piattaforma Azure ai log di monitoraggio di Azure, all'archiviazione di Azure o agli hub eventi di Azure.Create diagnostic settings using a Resource Manager template to forward Azure platform logs to Azure Monitor Logs, Azure storage, or Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672430"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Creare l'impostazione di diagnostica in Azure usando un modello di Resource ManagerCreate diagnostic setting in Azure using a Resource Manager template
[Le impostazioni di diagnostica](diagnostic-settings.md) in Monitoraggio di Azure specificano da dove inviare [i log della piattaforma](platform-logs-overview.md) raccolti dalle risorse di Azure e dalla piattaforma di Azure da cui dipendono. Questo articolo fornisce dettagli ed esempi per l'uso di un modello di [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) per creare e configurare le impostazioni di diagnostica per raccogliere i log della piattaforma in destinazioni diverse.

> [!NOTE]
> Poiché non è possibile [creare un'impostazione di diagnostica](diagnostic-settings.md) per il log attività di Azure usando PowerShell o l'interfaccia della riga di comando come le impostazioni di diagnostica per altre risorse di Azure, creare un modello di Resource Manager per il log attività usando le informazioni contenute in questo articolo e distribuire il modello usando PowerShell o l'interfaccia della riga di comando.

## <a name="deployment-methods"></a>Metodi di distribuzione
È possibile distribuire modelli di Resource Manager usando qualsiasi metodo valido, inclusi PowerShell e l'interfaccia della riga di comando. Le impostazioni di diagnostica per Log `az deployment create` attività `New-AzDeployment` devono essere distribuite in una sottoscrizione usando l'interfaccia della riga di comando o per PowerShell.Diagnostic settings for Activity log must deploy to a subscription using for CLI or for PowerShell. Le impostazioni di diagnostica per i `az group deployment create` log delle `New-AzResourceGroupDeployment` risorse devono essere distribuite in un gruppo di risorse usando l'interfaccia della riga di comando o powerShell.Diagnostic settings for resource logs must deploy to a resource group using for CLI or for PowerShell.

Per informazioni dettagliate, vedere Distribuire risorse con i modelli di [Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) e Distribuire le risorse con i modelli di Resource Manager [e l'interfaccia della riga di comando](../../azure-resource-manager/templates/deploy-cli.md) di Azure.See Deploy resources with Resource Manager templates and Azure Power to details. 





## <a name="resource-logs"></a>Log risorse
Per i log delle risorse, aggiungere una risorsa di tipo `<resource namespace>/providers/diagnosticSettings` al modello. La sezione delle proprietà segue il formato descritto in [Impostazioni di diagnostica - Crea o aggiorna](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Specificare `category` un `logs` nella sezione per ognuna delle categorie valide per la risorsa che si desidera raccogliere. Aggiungere `metrics` la proprietà per raccogliere le metriche delle risorse nelle stesse destinazioni se la [risorsa supporta le metriche.](metrics-supported.md)

Di seguito è riportato un modello che raccoglie una categoria di log delle risorse per una determinata risorsa in un'area di lavoro di Log Analytics, un account di archiviazione e un hub eventi.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Esempio
Di seguito è riportato un esempio che consente di creare un'impostazione di diagnostica per un'impostazione di scalabilità automatica che consente lo streaming di log delle risorse a un hub eventi, un account di archiviazione e un'area di lavoro di Log Analytics.Following is an example that creates a diagnostic setting for an autoscale setting that enables streaming of resource logs to an event hub, a storage account, and a Log Analytics workspace.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Log attività
Per il log attività di Azure, aggiungere una risorsa di tipo `Microsoft.Insights/diagnosticSettings`. Le categorie disponibili sono elencate in [Categorie nel Registro attività](activity-log-view.md#categories-in-the-activity-log). Di seguito è riportato un modello che raccoglie tutte le categorie del log attività in un'area di lavoro di Log Analytics, un account di archiviazione e un hub eventi.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [log della piattaforma sono disponibili in Azure.Read](platform-logs-overview.md)more about platform logs in Azure .
* Informazioni sulle [impostazioni di diagnostica](diagnostic-settings.md).
