---
title: Ritiro API monitoraggio di Azure
description: Descrive il ritiro delle versioni precedenti dell'API del provider di risorse OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744890"
---
# <a name="operationalinsights-api-version-retirement"></a>Ritiro versione API OperationalInsights
Microsoft fornisce notifiche per almeno 12 mesi prima del ritiro di un'API per semplificare la transizione a una versione più recente o supportata. È stata rilasciata una nuova versione (2020-08-01) per le API del provider di risorse **OperationalInsights** e tutte le versioni precedenti dell'API vengono ritirate il 31 ottobre 2023. Poiché le nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunte solo all'API corrente, è consigliabile eseguire l'aggiornamento alla versione più recente dell'API il prima possibile.

Dopo il 31 ottobre 2023, monitoraggio di Azure non supporterà più le versioni precedenti di API 2020-08-01. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalle versioni precedenti continueranno a essere gestite dal servizio monitoraggio di Azure fino al 31 ottobre 2023.

A seconda del metodo di configurazione usato, è necessario aggiornare la nuova versione nelle richieste REST e nei modelli di Gestione risorse per gli esempi seguenti:


## <a name="rest"></a>REST
Le richieste API REST usano la versione dell'API nell'URL della richiesta. Sostituire la versione con la versione più recente, come illustrato nell'esempio seguente.

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure Resource Manager
Gestione risorse modelli usano la versione dell'API nella proprietà **apiVersion** della risorsa. Sostituire la versione con la versione più recente, come illustrato nell'esempio seguente.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-08-01",
            "location": "westus",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "retentionInDays": 30,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
  }
}
```


## <a name="next-steps"></a>Passaggi successivi

- Vedere il [riferimento per l'API OperationalInsights](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions).
