---
title: Ritiro API monitoraggio di Azure
description: Descrive il ritiro delle versioni precedenti dell'API del provider di risorse OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 4075e27c3711cbc4c584005126b53f7c99942049
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926726"
---
# <a name="operationalinsights-api-version-retirement"></a>Ritiro versione API OperationalInsights
Microsoft fornisce notifiche per almeno 12 mesi prima del ritiro di un'API per semplificare la transizione a una versione più recente o supportata. È stata rilasciata una nuova versione (2020-08-01) per le API del provider di risorse **OperationalInsights** e tutte le versioni precedenti dell'API vengono ritirate il 29 febbraio 2014.

Si consiglia di iniziare a usare la versione 2020-08-01 ora per sfruttare i vantaggi delle nuove funzionalità, ad esempio il [cluster dedicato](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters), le [chiavi gestite dal cliente](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys), il [collegamento privato](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security) e l' [esportazione dei dati](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export). Inoltre, le nuove funzionalità e funzionalità e le ottimizzazioni vengono aggiunte solo all'API corrente.

Dopo il 29 febbraio 2014, monitoraggio di Azure non supporterà più le versioni precedenti delle API rispetto a 2020-08-01. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalle versioni precedenti continueranno a essere gestite dal servizio monitoraggio di Azure fino al 29 febbraio 2014.

## <a name="migration-steps"></a>Passaggi della migrazione
A seconda del metodo di configurazione usato, è necessario aggiornare la nuova versione nelle richieste **Rest** e nei **modelli gestione risorse** . Seguire gli esempi riportati di seguito per aggiornare la versione dell'API:

1. Le richieste API REST usano la versione dell'API nell'URL della richiesta. Sostituire tale versione con la versione più recente (2020-08-01), come illustrato nell'esempio seguente.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager modelli usano la versione dell'API nella proprietà **apiVersion** della risorsa. Sostituire tale versione con la versione più recente (2020-08-01), come illustrato nell'esempio seguente.


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

- Vedere le informazioni [di riferimento per l'API dell'area di lavoro OperationalInsights](/rest/api/loganalytics/workspaces).
