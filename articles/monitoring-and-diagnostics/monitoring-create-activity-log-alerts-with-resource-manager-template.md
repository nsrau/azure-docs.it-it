---
title: Creare un avviso di log attività con un modello di Resource Manager
description: Ricevere notifica della creazione di risorse di Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2017
ms.author: ancav
ms.component: alerts
ms.openlocfilehash: a1e28f08231ae1fbef3e0d0306e986c1dc9d1d1c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262991"
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Creare un avviso di log attività con un modello di Resource Manager
L'articolo descrive come usare un [modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) per configurare gli avvisi del log attività. Tramite i modelli è possibile configurare facilmente numerosi avvisi che vengono attivati in base a condizioni specifiche degli eventi del log attività nell'ambito del processo di distribuzione automatica.

I passaggi di base sono:

1. Creare un modello come file JSON che descrive come creare l'avviso del log attività.

2. [Distribuire il modello con un metodo di distribuzione qualsiasi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modello di Resource Manager per un avviso del log attività
Per creare un avviso del log attività usando un modello di Resource Manager, creare una risorsa di tipo `microsoft.insights/activityLogAlerts`. Compilare quindi tutte le proprietà correlate. Il modello seguente crea un avviso del log attività.

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
      "apiVersion": "2017-04-01",
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

Per alcuni esempi di modelli di avvisi del log attività, visitare la [raccolta di modelli di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights).

> [!NOTE]

> È anche possibile creare regole di avviso del log attività usando l'esperienza utente migliorata in Monitoraggio > [Avvisi (anteprima)](monitoring-overview-unified-alerts.md). Per altre informazioni su come crearle, vedere [questo articolo](monitoring-activity-log-alerts-new-experience.md).

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sugli avvisi](monitoring-overview-alerts.md).
- Informazioni su come aggiungere [gruppi di azione usando un modello di Resource Manager](monitoring-create-action-group-with-resource-manager-template.md).
- Informazioni su come [creare un avviso del log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Informazioni su come [creare un avviso di log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
