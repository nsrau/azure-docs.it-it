---
title: Ottenere le modifiche delle risorse
description: Informazioni su come capire quando una risorsa è stata modificata, ottenere un elenco delle proprietà modificate e valutare le differenze.
ms.date: 08/10/2020
ms.topic: how-to
ms.openlocfilehash: 02dfd2b8b66cd09be39a44166a0fd62bb13c8395
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056568"
---
# <a name="get-resource-changes"></a>Ottenere le modifiche delle risorse

Le risorse vengono modificate nel corso dell'utilizzo giornaliero, della riconfigurazione e persino della ridistribuzione.
Le modifiche possono essere apportate da un singolo utente o da un processo automatizzato. La maggior parte delle modifiche è prevista da progettazione, ma non tutte. Con gli ultimi 14 giorni di cronologia delle modifiche, Azure Resource Graph consente di:

- Capire quando sono state rilevate modifiche in una proprietà Azure Resource Manager
- Per ogni modifica di risorsa, visualizzare i dettagli delle modifiche delle proprietà
- Vedere un confronto completo della risorsa prima e dopo la modifica rilevata

Il rilevamento delle modifiche e i dettagli sono utili per gli scenari di esempio seguenti:

- Durante la gestione degli eventi imprevisti per comprendere le modifiche _potenzialmente_ correlate. Eseguire una query per gli eventi di modifica durante un intervallo di tempo specifico e valutare i dettagli della modifica.
- Mantenere aggiornato un database di gestione della configurazione, noto come CMDB. Anziché aggiornare tutte le risorse e i relativi set di proprietà completi in base a una frequenza pianificata, ottenere solo le modifiche.
- Capire quali altre proprietà potrebbero essere state modificate quando una risorsa ha cambiato lo stato di conformità. La valutazione di queste proprietà aggiuntive può fornire dati analitici su altre proprietà che potrebbero dover essere gestite tramite una definizione di Criteri di Azure.

Questo articolo illustra come raccogliere queste informazioni tramite l'SDK di Resource Graph. Per visualizzare queste informazioni nel portale di Azure, vedere [Cronologia modifiche](../../policy/how-to/determine-non-compliance.md#change-history) di Criteri di Azure oppure [Cronologia modifiche](../../../azure-monitor/platform/activity-log.md#view-the-activity-log) del log attività di Azure. Per informazioni dettagliate sulle modifiche apportate alle applicazioni dal livello infrastruttura fino alla distribuzione delle applicazioni, vedere [Usare Analisi delle modifiche alle applicazioni (anteprima) in Monitoraggio di Azure](../../../azure-monitor/app/change-analysis.md).

> [!NOTE]
> I dettagli delle modifiche in Resource Graph sono relativi alle proprietà di Resource Manager. Per tenere traccia delle modifiche all'interno di una macchina virtuale, vedere il [rilevamento modifiche](../../../automation/change-tracking.md) di Automazione di Azure o la [configurazione guest per le macchine virtuali](../../policy/concepts/guest-configuration.md) di Criteri di Azure.

> [!IMPORTANT]
> La cronologia delle modifiche in Azure Resource Graph è in anteprima pubblica.

## <a name="find-detected-change-events-and-view-change-details"></a>Trovare gli eventi di modifica rilevati e visualizzare i dettagli delle modifiche

Il primo passaggio per visualizzare le modifiche apportate a una risorsa consiste nel trovare gli eventi di modifica correlati a tale risorsa in un intervallo di tempo. Ogni evento di modifica include anche informazioni dettagliate sulle modifiche apportate alla risorsa. Questo passaggio viene eseguito tramite l'endpoint REST **resourceChanges**.

L'endpoint **resourceChanges** accetta i parametri seguenti nel corpo della richiesta:

- **resourceId** \[obbligatorio\]: la risorsa di Azure in cui cercare le modifiche.
- **interval** \[obbligatorio\]: una proprietà con date di _inizio_ e _fine_ in cui cercare un evento di modifica utilizzando l'**ora Zulu (Z)** .
- **fetchPropertyChanges** (facoltativo): una proprietà booleana che indica se l'oggetto risposta include modifiche alle proprietà.

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

La risposta è simile all'esempio seguente:

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

- **changeId**: questo valore è univoco per la risorsa. Anche se la stringa **changeId** può talvolta contenere altre proprietà, è garantita come univoca.
- **beforeSnapshot**: contiene gli elementi **snapshotId** e **timestamp** dello snapshot della risorsa acquisito prima del rilevamento di una modifica.
- **afterSnapshot**: contiene gli elementi **snapshotId** e **timestamp** dello snapshot della risorsa acquisito dopo il rilevamento di una modifica.
- **changeType**: descrive il tipo di modifica rilevata per l'intero record delle modifiche tra **beforeSnapshot** e **afterSnapshot**. I valori possibili sono: _Create_, _Update_ e _Delete_. La matrice di proprietà **propertyChanges** è inclusa solo quando **changeType** è di tipo _Update_.
- **propertyChanges**: questa matrice di proprietà descrive in dettaglio tutte le proprietà delle risorse aggiornate tra **beforeSnapshot** e **afterSnapshot**:
  - **propertyName**: il nome della proprietà della risorsa modificata.
  - **changeCategory**: descrive la modifica apportata. I valori possibili sono: _System_ e _User_.
  - **changeType**: descrive il tipo di modifica rilevata per la singola proprietà della risorsa.
    I valori possibili sono: _Insert_, _Update_, _Remove_.
  - **beforeValue**: il valore della proprietà della risorsa in **beforeSnapshot**. Non viene visualizzato se **changeType** è impostato su _Insert_.
  - **afterValue**: il valore della proprietà della risorsa in **afterSnapshot**. Non viene visualizzato se **changeType** è impostato su _Remove_.

## <a name="compare-resource-changes"></a>Confrontare le modifiche delle risorse

Con **changeId** dell'endpoint **resourceChanges**, l'endpoint REST **resourceChangeDetails** viene quindi usato per ottenere gli snapshot precedenti e successivi della risorsa modificata.

L'endpoint **resourceChangeDetails** richiede due parametri nel corpo della richiesta:

- **resourceId**: la risorsa di Azure su cui confrontare le modifiche.
- **changeId**: l'evento di modifica univoco per l'elemento **resourceId** raccolto da **resourceChanges**.

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per **resourceChangeDetails** è:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La risposta è simile all'esempio seguente:

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

**beforeSnapshot** e **afterSnapshot** forniscono entrambi l'ora in cui lo snapshot è stato acquisito e le proprietà in quel momento. La modifica si è verificata in un determinato punto tra questi snapshot. Esaminando l'esempio precedente, è possibile notare che la proprietà modificata è **supportsHttpsTrafficOnly**.

Per confrontare i risultati, usare la proprietà **changes** in **resourceChanges** oppure valutare la parte **content** di ogni snapshot in **resourceChangeDetails** per determinare la differenza. Se si confrontano gli snapshot, il valore **timestamp** viene sempre visualizzato come differenza, nonostante sia previsto.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](../concepts/explore-resources.md).
- Per informazioni sull'uso di query a frequenza elevata, vedere [Istruzioni per le richieste con limitazioni](../concepts/guidance-for-throttled-requests.md).
