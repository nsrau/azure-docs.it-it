---
title: Creare un'impostazione di diagnostica in Azure usando Gestione risorse modello
description: Creare impostazioni di diagnostica usando un modello di Gestione risorse per inviare i log della piattaforma Azure ai log di monitoraggio di Azure, archiviazione di Azure o hub eventi di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: b549cc0e890a122a04984baa2348831fc51abe08
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531004"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Creare un'impostazione di diagnostica in Azure usando un modello di Gestione risorse
[Le impostazioni di diagnostica](diagnostic-settings.md) in monitoraggio di Azure specificano dove inviare i [log della piattaforma](platform-logs-overview.md) raccolti dalle risorse di Azure e dalla piattaforma Azure da cui dipendono. Questo articolo fornisce informazioni ed esempi per l'uso di un [modello di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) per creare e configurare le impostazioni di diagnostica per raccogliere i log della piattaforma in destinazioni diverse. 

> [!NOTE]
> Poiché non è possibile [creare un'impostazione di diagnostica](diagnostic-settings.md) per il log attività di Azure usando PowerShell o l'interfaccia della riga di comando come impostazioni di diagnostica per altre risorse di Azure, creare un modello di gestione risorse per il log attività usando le informazioni contenute in questo articolo e distribuire il modello usando PowerShell o l'interfaccia della riga di comando.

## <a name="deployment-methods"></a>Metodi di distribuzione
È possibile distribuire Gestione risorse modelli usando un metodo valido, tra cui PowerShell e l'interfaccia della riga di comando. Le impostazioni di diagnostica per log attività devono essere distribuite in una sottoscrizione usando `az deployment create` per l'interfaccia della riga di comando o `New-AzDeployment` per PowerShell. Le impostazioni di diagnostica per i log delle risorse devono essere distribuite in un gruppo di risorse usando `az group deployment create` per CLI o `New-AzResourceGroupDeployment` per PowerShell. 

Per informazioni dettagliate, vedere [distribuire risorse con modelli di gestione risorse e Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [distribuire risorse con modelli di gestione risorse e l'interfaccia](../../azure-resource-manager/resource-group-template-deploy-cli.md) della riga di comando di Azure. 





## <a name="resource-logs"></a>Log risorse
Per i log delle risorse, aggiungere una risorsa di tipo `<resource namespace>/providers/diagnosticSettings` al modello. La sezione properties segue il formato descritto in [impostazioni di diagnostica-crea o aggiorna](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). Fornire un `category` nella sezione `logs` per ognuna delle categorie valide per la risorsa che si desidera raccogliere. Aggiungere la proprietà `metrics` per raccogliere le metriche delle risorse nelle stesse destinazioni se la [risorsa supporta le metriche](metrics-supported.md).

Di seguito è riportato un modello che raccoglie una categoria di log delle risorse per una determinata risorsa in un'area di lavoro Log Analytics, un account di archiviazione e un hub eventi. 

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
Di seguito è riportato un esempio che crea un'impostazione di diagnostica per un'impostazione di scalabilità automatica che consente lo streaming di log di risorse in un hub eventi, un account di archiviazione e un'area di lavoro Log Analytics.

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
Per il log attività di Azure, aggiungere una risorsa di tipo `Microsoft.Insights/diagnosticSettings`. Le categorie disponibili sono elencate in [categorie nel log attività](activity-log-view.md#categories-in-the-activity-log). Di seguito è riportato un modello che raccoglie tutte le categorie di log attività in un'area di lavoro Log Analytics, un account di archiviazione e un hub eventi.


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
* Scopri di più sui [log della piattaforma in Azure](platform-logs-overview.md).
* Informazioni sulle [impostazioni di diagnostica](diagnostic-settings.md).
