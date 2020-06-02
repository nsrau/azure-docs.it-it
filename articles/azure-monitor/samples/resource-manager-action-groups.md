---
title: Esempi di modelli di Azure Resource Manager per gruppi di azioni
description: Modelli di Azure Resource Manager di esempio per distribuire gruppi di azioni di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: fda9a7e39f1bb103a203cdfa61a5044c51c692f2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83852974"
---
# <a name="resource-manager-template-samples-for-action-groups-in-azure-monitor"></a>Esempi di modelli di Resource Manager per i gruppi di azioni in Monitoraggio di Azure
Questo articolo include esempi di [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) per creare [gruppi di azioni](../platform/action-groups.md) in Monitoraggio di Azure. Ogni esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-an-action-group"></a>Creare un gruppo di azioni
L'esempio seguente crea un gruppo di azioni.


### <a name="template-file"></a>File modello

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name within the resource group for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name up to 12 characters for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "actionGroupName": {
        "value": "My Action Group"
      },
      "actionGroupShortName": {
        "value": "mygroup"
      }
  }
}
```



## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](resource-manager-samples.md).
* [Altre informazioni sui gruppi di azioni](../platform/action-groups.md).

