---
title: Ottenere le modifiche delle risorse
description: Informazioni su come individuare quando una risorsa è stata modificata e ottenere un elenco delle proprietà modificate.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980310"
---
# <a name="get-resource-changes"></a>Ottenere le modifiche delle risorse

Le risorse vengono modificate nel corso dell'utilizzo giornaliero, della riconfigurazione e persino della ridistribuzione.
La modifica può provenire da un singolo utente o da un processo automatizzato. La maggior parte delle modifiche è la progettazione, ma a volte non lo è. Con gli ultimi 14 giorni di cronologia delle modifiche, il grafico delle risorse di Azure consente di:

- Scoprire quando sono state rilevate modifiche a una proprietà di Azure Resource Manager.
- Visualizzare le proprietà modificate come parte di tale evento di modifica.

Il rilevamento delle modifiche e i dettagli sono utili per gli scenari di esempio seguenti:

- Durante la gestione degli eventi imprevisti per comprendere le modifiche _potenzialmente_ correlate. Eseguire una query per gli eventi di modifica durante un intervallo di tempo specifico e valutare i dettagli della modifica.
- Mantenere un database di gestione della configurazione, noto come CMDB, aggiornato. Anziché aggiornare tutte le risorse e i relativi set di proprietà completi in base a una frequenza pianificata, ottenere solo le modifiche.
- Informazioni sulle altre proprietà che potrebbero essere state modificate quando una risorsa ha modificato lo stato di conformità. La valutazione di queste proprietà aggiuntive può fornire informazioni approfondite sulle altre proprietà che potrebbero dover essere gestite tramite una definizione di criteri di Azure.

Questo articolo illustra come raccogliere queste informazioni tramite l'SDK di Resource Graph. Per visualizzare queste informazioni nella portale di Azure, vedere [cronologia delle modifiche](../../policy/how-to/determine-non-compliance.md#change-history-preview) di criteri di Azure o [cronologia modifiche](../../../azure-monitor/platform/activity-log-view.md#azure-portal)del log attività di Azure.

> [!NOTE]
> I dettagli delle modifiche nel grafico risorse sono per le proprietà Gestione risorse. Per tenere traccia delle modifiche all'interno di una macchina virtuale, vedere [rilevamento modifiche](../../../automation/automation-change-tracking.md) di automazione di Azure o configurazione Guest di criteri di Azure [per le macchine virtuali](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> La cronologia delle modifiche in Azure Resource Graph è in anteprima pubblica.

## <a name="find-when-changes-were-detected"></a>Individuare le modifiche rilevate

Il primo passaggio per vedere le modifiche apportate a una risorsa consiste nel trovare gli eventi di modifica correlati a tale risorsa in un intervallo di tempo. Questo passaggio viene eseguito tramite l'endpoint REST di **resourceChanges** .

L'endpoint **resourceChanges** richiede due parametri nel corpo della richiesta:

- **resourceId**: Risorsa di Azure in cui cercare le modifiche.
- **interval**: Proprietà con date di _inizio_ e di _fine_ per il momento in cui verificare la presenza di un evento di modifica utilizzando il **fuso orario Zulu (Z)** .

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per **resourceChanges** è:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

La risposta ha un aspetto simile all'esempio seguente:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Ogni evento di modifica rilevato per **resourceId** dispone di un **changeId** univoco per tale risorsa. Sebbene la stringa **changeId** possa talvolta contenere altre proprietà, è garantito che sia univoca. Il record delle modifiche include le ore in cui sono stati eseguiti gli snapshot before e After.
L'evento di modifica si è verificato in un determinato momento in questo intervallo di tempo.

## <a name="see-what-properties-changed"></a>Vedere quali proprietà sono state modificate

Con **changeId** dall'endpoint **resourceChanges** , l'endpoint REST **resourceChangeDetails** viene quindi usato per ottenere le specifiche dell'evento di modifica.

L'endpoint **resourceChangeDetails** richiede due parametri nel corpo della richiesta:

- **resourceId**: Risorsa di Azure in cui cercare le modifiche.
- **changeId**: Evento di modifica univoco per **resourceId** raccolto da **resourceChanges**.

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per **resourceChangeDetails** è:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La risposta ha un aspetto simile all'esempio seguente:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** e **afterSnapshot** forniscono ogni volta che lo snapshot è stato utilizzato e le proprietà in quel momento. La modifica si è verificata in un determinato punto tra gli snapshot. Esaminando l'esempio precedente, è possibile notare che la proprietà modificata è **supportsHttpsTrafficOnly**.

Per confrontare i risultati a livello di codice, confrontare la parte del **contenuto** di ogni snapshot per determinare la differenza. Se si confronta l'intero snapshot, il **timestamp** viene sempre visualizzato come differenza nonostante sia previsto.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query Starter](../samples/starter.md).
- Vedere uso avanzato nelle [query avanzate](../samples/advanced.md).
- Scopri come [esplorare le risorse](../concepts/explore-resources.md).