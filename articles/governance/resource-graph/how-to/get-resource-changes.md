---
title: Ottenere le modifiche delle risorse
description: Comprendere come trovare quando una risorsa è stata modificata, ottenere un elenco delle proprietà modificate e valutare le differenze.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873030"
---
# <a name="get-resource-changes"></a>Ottenere le modifiche delle risorse

Le risorse vengono modificate nel corso dell'uso quotidiano, della riconfigurazione e persino della ridistribuzione.
Il cambiamento può provenire da un individuo o da un processo automatizzato. La maggior parte del cambiamento è in base alla progettazione, ma a volte non lo è. Con gli ultimi 14 giorni di cronologia delle modifiche, Azure Resource Graph consente di:With the last 14 days of change history, Azure Resource Graph enables you to:

- Individuare quando sono state rilevate modifiche in una proprietà di Azure Resource ManagerFind when changes were detected on an Azure Resource Manager property
- Per ogni modifica delle risorse, vedere i dettagli della modifica delle proprietàFor each resource change, see property change details
- Visualizzare un confronto completo della risorsa prima e dopo la modifica rilevata

Il rilevamento delle modifiche e i dettagli sono utili per gli scenari di esempio seguenti:Change detection and details are valuable for the following example scenarios:

- Durante la gestione degli incidenti per comprendere le modifiche _potenzialmente_ correlate. Eseguire una query per gli eventi di modifica durante un intervallo di tempo specifico e valutare i dettagli della modifica.
- Mantenere aggiornato un database di gestione della configurazione, noto come CMDB. Anziché aggiornare tutte le risorse e i relativi set di proprietà completi con una frequenza pianificata, ottenere solo le modifiche.
- Informazioni sulle altre proprietà che potrebbero essere state modificate quando una risorsa ha modificato lo stato di conformità. La valutazione di queste proprietà aggiuntive può fornire informazioni dettagliate su altre proprietà che potrebbe essere necessario gestire tramite una definizione di criteri di Azure.Evaluation of these additional properties can provide insights into other properties that may need to be managed via an Azure Policy definition.

This article shows how to gather this information through Resource Graph's SDK. Per visualizzare queste informazioni nel portale di Azure, vedere Cronologia delle modifiche di Criteri di Azure o Cronologia delle modifiche del log attività di Azure.To see this information in the Azure portal, see Azure Policy's [Change history](../../policy/how-to/determine-non-compliance.md#change-history-preview) or Azure Activity Log [Change history](../../../azure-monitor/platform/activity-log-view.md#azure-portal).
Per informazioni dettagliate sulle modifiche apportate alle applicazioni dal livello di infrastruttura fino alla distribuzione delle applicazioni, vedere Usare l'analisi delle modifiche delle applicazioni (anteprima) in Monitoraggio di Azure.For details about changes to your applications from the infrastructure layer to the application deployment, see [Use Application Change Analysis (preview)](../../../azure-monitor/app/change-analysis.md) in Azure Monitor.

> [!NOTE]
> I dettagli delle modifiche in Resource Graph sono relativi alle proprietà di Resource Manager.Change details in Resource Graph are for Resource Manager properties. Per tenere traccia delle modifiche all'interno di una macchina virtuale, vedere [Rilevamento delle](../../../automation/automation-change-tracking.md) modifiche di Automazione di Azure o Configurazione guest di Criteri di Azure [per le macchine virtuali.](../../policy/concepts/guest-configuration.md)

> [!IMPORTANT]
> La cronologia delle modifiche in Azure Resource Graph è in Anteprima pubblica.

## <a name="find-detected-change-events-and-view-change-details"></a>Trovare gli eventi di modifica rilevati e visualizzare i dettagli delle modifiche

Il primo passaggio per vedere cosa è cambiato in una risorsa consiste nell'individuare gli eventi di modifica correlati a tale risorsa in un intervallo di tempo. Ogni evento di modifica include anche i dettagli sulle modifiche apportate alla risorsa. Questo passaggio viene eseguito tramite l'endpoint REST **resourceChanges.This** step is done through the resourceChanges REST endpoint.

L'endpoint resourceChanges accetta i parametri seguenti nel corpo della richiesta:The **resourceChanges** endpoint accepts the following parameters in the request body:

- **resourceId** \[\]required: risorsa di Azure in cui cercare le modifiche.
- **intervallo** \[\]richiesto : Una proprietà con date di _inizio_ e _di fine_ per quando controllare la presenza di un evento di modifica utilizzando il fuso **orario zulu (sezione)**.
- **fetchPropertyChanges** (facoltativo): proprietà booleana che imposta se l'oggetto risposta include modifiche alle proprietà.

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

Con il corpo della richiesta precedente, l'URI dell'API REST per resourceChanges è:With the above request body, the REST API URI for **resourceChanges** is:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

La risposta è simile a questa:

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

Ogni evento di modifica rilevato per resourceId ha le proprietà seguenti:Each detected change event for the **resourceId** has the following properties:

- **changeId** - Questo valore è univoco per la risorsa.ChangeId - This value is unique to that resource. Mentre la stringa **changeId** può talvolta contenere altre proprietà, è garantito solo per essere univoco.
- **beforeSnapshot** - Contiene lo **snapshotId** e il **timestamp** dello snapshot della risorsa creato prima che venisse rilevata una modifica.
- **afterSnapshot** - Contiene lo **snapshotId** e il **timestamp** dello snapshot della risorsa creato dopo il rilevamento di una modifica.
- **changeType** - Descrive il tipo di modifica rilevata per l'intero record di modifica tra **beforeSnapshot** e **afterSnapshot**. I valori sono: _Create_, _Update_ed _Delete_. La matrice di proprietà **propertyChanges** viene inclusa solo quando **changeType** è _Update_.
- **propertyChanges** - Questa matrice di proprietà descrive in dettaglio tutte le proprietà della risorsa aggiornate tra **beforeSnapshot** e **afterSnapshot**:
  - **propertyName** - Il nome della proprietà della risorsa che è stata modificata.
  - **changeCategory** - Descrive cosa ha apportato la modifica. I valori sono: _Sistema_ e _Utente_.
  - **changeType** - Descrive il tipo di modifica rilevata per la singola proprietà della risorsa.
    I valori sono: _Insert_, _Update_, _Remove_.
  - **beforeValue** - Il valore della proprietà della risorsa in **beforeSnapshot**. Non viene visualizzato quando **changeType** è _Insert_.
  - **afterValue** - Il valore della proprietà della risorsa in **afterSnapshot**. Non viene visualizzato quando **changeType** è _Remove_.

## <a name="compare-resource-changes"></a>Confrontare le modifiche alle risorse

Con **changeId** dall'endpoint **resourceChanges,** l'endpoint REST **resourceChangeDetails** viene quindi usato per ottenere gli snapshot prima e dopo della risorsa modificata.

L'endpoint resourceChangeDetails richiede due parametri nel corpo della richiesta:The **resourceChangeDetails** endpoint requires two parameters in the request body:

- **resourceId**: la risorsa di Azure in cui confrontare le modifiche.
- **changeId**: l'evento di modifica univoco per **resourceId** raccolto da **resourceChanges**.

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per resourceChangeDetails è:With the above request body, the REST API URI for **resourceChangeDetails** is:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La risposta è simile a questa:

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

**beforeSnapshot** e **afterSnapshot** forniscono ciascuno l'ora in cui è stata scattata la istantanea e le proprietà in quel momento. La modifica è avvenuta a un certo punto tra queste istantanee. Esaminando l'esempio precedente, è possibile vedere che la proprietà modificata è **supportsHttpsTrafficOnly**.

Per confrontare i risultati, usare la proprietà **changes** in **resourceChanges** o valutare la parte di **contenuto** di ogni snapshot in **resourceChangeDetails** per determinare la differenza. Se si confrontano gli snapshot, il **timestamp** viene sempre visualizzato come differenza nonostante sia previsto.

## <a name="next-steps"></a>Passaggi successivi

- Vedere la lingua in uso nelle [query Starter](../samples/starter.md).
- Vedere Utilizzo avanzato nelle [query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](../concepts/explore-resources.md).