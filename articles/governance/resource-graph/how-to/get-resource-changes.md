---
title: Ottenere le modifiche delle risorse
description: Informazioni su come individuare quando una risorsa è stata modificata e ottenere un elenco delle proprietà modificate.
ms.date: 10/09/2019
ms.topic: conceptual
ms.openlocfilehash: 25d900276518e51e80021d1fdb1ac80945eb2d5c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958753"
---
# <a name="get-resource-changes"></a>Ottenere le modifiche delle risorse

Le risorse vengono modificate a causa di uso quotidiano, riconfigurazione e anche ridistribuzione.
La modifica può provenire da un singolo utente o da un processo automatizzato. La maggior parte delle modifiche è la progettazione, ma a volte non lo è. Con gli ultimi 14 giorni di cronologia delle modifiche, il grafico delle risorse di Azure consente di:

- Individuare quando sono state rilevate modifiche in una proprietà Azure Resource Manager
- Per ogni modifica della risorsa, vedere dettagli delle modifiche delle proprietà
- Vedere un confronto completo della risorsa prima e dopo la modifica rilevata

Il rilevamento delle modifiche e i dettagli sono utili per gli scenari di esempio seguenti:

- Durante la gestione degli eventi imprevisti per comprendere le modifiche _potenzialmente_ correlate. Eseguire una query per gli eventi di modifica durante un intervallo di tempo specifico e valutare i dettagli della modifica.
- Mantenere un database di gestione della configurazione, noto come CMDB, aggiornato. Anziché aggiornare tutte le risorse e i relativi set di proprietà completi in base a una frequenza pianificata, ottenere solo le modifiche.
- Informazioni sulle altre proprietà che potrebbero essere state modificate quando una risorsa ha modificato lo stato di conformità. La valutazione di queste proprietà aggiuntive può fornire informazioni approfondite sulle altre proprietà che potrebbero dover essere gestite tramite una definizione di criteri di Azure.

Questo articolo illustra come raccogliere queste informazioni tramite l'SDK di Resource Graph. Per visualizzare queste informazioni nella portale di Azure, vedere [cronologia delle modifiche](../../policy/how-to/determine-non-compliance.md#change-history-preview) di criteri di Azure o [cronologia modifiche](../../../azure-monitor/platform/activity-log-view.md#azure-portal)del log attività di Azure.
Per informazioni dettagliate sulle modifiche apportate alle applicazioni dal livello dell'infrastruttura fino alla distribuzione delle applicazioni, vedere [usare l'analisi delle modifiche dell'applicazione (anteprima)](../../../azure-monitor/app/change-analysis.md) in monitoraggio di Azure.

> [!NOTE]
> I dettagli delle modifiche nel grafico risorse sono per le proprietà Gestione risorse. Per tenere traccia delle modifiche all'interno di una macchina virtuale, vedere [rilevamento modifiche](../../../automation/automation-change-tracking.md) di automazione di Azure o configurazione Guest di criteri di Azure [per le macchine virtuali](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> La cronologia delle modifiche in Azure Resource Graph è in anteprima pubblica.

## <a name="find-detected-change-events-and-view-change-details"></a>Trovare gli eventi di modifica rilevati e visualizzare i dettagli delle modifiche

Il primo passaggio per vedere le modifiche apportate a una risorsa consiste nel trovare gli eventi di modifica correlati a tale risorsa in un intervallo di tempo. Ogni evento di modifica include anche informazioni dettagliate sulle modifiche apportate alla risorsa. Questo passaggio viene eseguito tramite l'endpoint REST di **resourceChanges** .

L'endpoint **resourceChanges** accetta i parametri seguenti nel corpo della richiesta:

- **resourceId** \[required\]: la risorsa di Azure in cui cercare le modifiche.
- **intervallo** \[necessario\]: una proprietà con date di _inizio_ e di _fine_ per il momento in cui verificare la presenza di un evento di modifica usando il **fuso orario Zulu (Z)** .
- **fetchPropertyChanges** (facoltativo): Proprietà booleana che imposta se l'oggetto risposta include le modifiche alle proprietà.

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per **resourceChanges** è:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

La risposta ha un aspetto simile all'esempio seguente:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Ogni evento di modifica rilevato per **resourceId** presenta le proprietà seguenti:

- **changeId** : questo valore è univoco per la risorsa. Sebbene la stringa **changeId** possa talvolta contenere altre proprietà, è garantito che sia univoca.
- **beforeSnapshot** : contiene il **snapshotId** e il **timestamp** dello snapshot di risorsa che è stato effettuato prima che venisse rilevata una modifica.
- **afterSnapshot** : contiene l' **snapshotId** e il **timestamp** dello snapshot di risorsa che è stato eseguito dopo il rilevamento di una modifica.
- **ChangeType** : descrive il tipo di modifica rilevata per l'intero record delle modifiche tra **beforeSnapshot** e **afterSnapshot**. I valori sono: _create_, _Update_e _Delete_. La matrice di proprietà **propertyChanges** viene inclusa solo quando **ChangeType** è _Update_.
- **propertyChanges** : questa matrice di proprietà descrive in dettaglio tutte le proprietà delle risorse aggiornate tra **beforeSnapshot** e **afterSnapshot**:
  - **PropertyName** : nome della proprietà della risorsa che è stata modificata.
  - **changeCategory** : descrive la modifica apportata. I valori sono: _System_ e _User_.
  - **ChangeType** : descrive il tipo di modifica rilevata per la singola proprietà della risorsa.
    I valori sono: _Insert_, _Update_, _Remove_.
  - **beforeValue** : valore della proprietà Resource in **beforeSnapshot**. Non viene visualizzato quando **ChangeType** è _Insert_.
  - **afterValue** : valore della proprietà Resource in **afterSnapshot**. Non viene visualizzato quando **ChangeType** è _Remove_.

## <a name="compare-resource-changes"></a>Confrontare le modifiche delle risorse

Con **changeId** dall'endpoint **resourceChanges** , l'endpoint REST **resourceChangeDetails** viene quindi usato per ottenere gli snapshot before e After della risorsa che è stata modificata.

L'endpoint **resourceChangeDetails** richiede due parametri nel corpo della richiesta:

- **resourceId**: risorsa di Azure in cui confrontare le modifiche.
- **changeId**: evento di modifica univoco per il **resourceId** raccolto da **resourceChanges**.

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

Per confrontare i risultati, usare la proprietà **changes** in **resourceChanges** o valutare la parte del **contenuto** di ogni snapshot in **resourceChangeDetails** per determinare la differenza. Se si confrontano gli snapshot, il **timestamp** viene sempre visualizzato come differenza nonostante sia previsto.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query Starter](../samples/starter.md).
- Vedere uso avanzato nelle [query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](../concepts/explore-resources.md).