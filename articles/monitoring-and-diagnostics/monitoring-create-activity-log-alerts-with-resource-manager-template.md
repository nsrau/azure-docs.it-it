---
title: "Creare un avviso di log attività con un modello di Resource Manager | Microsoft Docs"
description: Ricevere notifica della creazione di risorse di Azure.
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Creare un avviso di log attività con un modello di Resource Manager
L'articolo descrive come usare un [modello di Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) per configurare gli avvisi del log attività. Il modello consente di configurare automaticamente gli avvisi relativi alle risorse al momento della loro creazione nell'ambito del processo di distribuzione automatizzata.

I passaggi di base sono i seguenti:

1.    Creare un modello come file JSON che descrive come creare l'avviso del log attività.
2.    [Distribuire il modello con un metodo di distribuzione qualsiasi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

La sezione seguente descrive come creare un modello di Resource Manager prima solo per un avviso del log attività e poi per un avviso del log attività durante la creazione di un'altra risorsa.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modello di Resource Manager per un avviso del log attività
Per creare un avviso del log attività usando un modello di Resource Manager, creare una risorsa di tipo `microsoft.insights/activityLogAlerts` e completare tutte le proprietà correlate. Il modello seguente crea un avviso del log attività.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sugli [Avvisi](monitoring-overview-alerts.md)  
Come aggiungere [gruppi di azione usando un modello di Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

