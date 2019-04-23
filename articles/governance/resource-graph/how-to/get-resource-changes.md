---
title: Ottenere le modifiche alle risorse
description: Informazioni su come individuare quando è stata modificata una risorsa e ottenere un elenco delle proprietà che è stato modificato.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: f4618e945db443e8d7cf9fdcc49e20e5a09ebd39
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014097"
---
# <a name="get-resource-changes"></a>Ottenere le modifiche alle risorse

Ottengano trasformare le risorse tramite il corso di utilizzo giornaliero, riconfigurazione e persino la ridistribuzione.
Modifica può provenire da un utente o da un processo automatizzato. La maggior parte delle modifiche è per impostazione predefinita, ma in alcuni casi non lo è. Con gli ultimi 14 giorni della cronologia delle modifiche, Graph risorse di Azure consente di:

- Trovare le modifiche sono state rilevate su una proprietà di Azure Resource Manager.
- Vedere quali le proprietà modificate come parte di tale evento di modifica.

Rilevamento delle modifiche e dettagli sono utili per gli scenari di esempio seguenti:

- Durante la gestione degli eventi imprevisti per comprendere _potenzialmente_ modifiche correlate. Eseguire una query per gli eventi di modifica durante un intervallo di tempo specifico e valutare i dettagli di modifica.
- Mantenere un Database di gestione, noto come un database CMDB, aggiornato. Anziché aggiornare tutte le risorse e i relativi set di proprietà completo in base a una frequenza pianificata, ottenere solo le modifiche apportate.
- Informazioni su quali altre proprietà sono stati modificati quando una risorsa modificato lo stato di conformità. Valutazione di queste proprietà aggiuntive può offrire informazioni dettagliate sulle altre proprietà che potrebbero dover essere gestito tramite una definizione di criteri di Azure.

Questo articolo illustra come raccogliere tali informazioni tramite SDK di risorsa del grafico. Per visualizzare queste informazioni nel portale di Azure, vedere criteri di Azure [cronologia modifiche](../../policy/how-to/determine-non-compliance.md#change-history-preview).

> [!NOTE]
> Dettagli di modifica nella risorsa Graph sono per le proprietà di Resource Manager. Per tenere traccia delle modifiche all'interno di una macchina virtuale, vedere l'automazione di Azure [rilevamento modifiche](../../../automation/automation-change-tracking.md) o criteri di Azure [configurazione Guest per macchine virtuali](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Cronologia delle modifiche nel grafico di risorse di Azure è disponibile in anteprima pubblica.

## <a name="find-when-changes-were-detected"></a>Individuare quando sono state rilevate modifiche

Il primo passaggio per visualizzare le modifiche apportate in una risorsa è per trovare gli eventi di modifica correlati a tale risorsa in un intervallo di tempo. Questo passaggio viene eseguito tramite il [resourceChanges](/rest/api/azureresourcegraph/resourceChanges) endpoint REST.

Il **resourceChanges** endpoint richiede due parametri nel corpo della richiesta:

- **resourceId**: La risorsa di Azure per cercare le modifiche.
- **interval**: Una proprietà con _avviare_ e _finali_ date di cui si vuole verificare la presenza di un evento di modifica utilizzando il **Zulu fuso orario (Z)**.

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

La risposta sarà simile a questo esempio:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
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

Ognuno ha rilevato evento di modifica per il **resourceId** ha una **changeId** univoca per tale risorsa. Mentre il **changeId** stringa in alcuni casi può contenere altre proprietà, ha solo garantito come univoco. Il record di modifica include le volte che la prima e dopo che sono stati creati snapshot.
Si è verificato l'evento di modifica a un certo punto in questa finestra di tempo.

## <a name="see-what-properties-changed"></a>Vedere le novità delle proprietà

Con il **changeId** dalle **resourceChanges** endpoint, il [resourceChangeDetails](/rest/api/azureresourcegraph/resourceChangeDetails) endpoint REST viene quindi usato per ottenere informazioni specifiche dell'evento di modifica.

Il **resourceChangeDetails** endpoint richiede due parametri nel corpo della richiesta:

- **resourceId**: La risorsa di Azure per cercare le modifiche.
- **changeId**: L'evento di modifica univoco per il **resourceId** raccolto dal **resourceChanges**.

Corpo della richiesta di esempio:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
    }
}
```

Con il corpo della richiesta precedente, l'URI dell'API REST per **resourceChangeDetails** è:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

La risposta sarà simile a questo esempio:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** e **afterSnapshot** ognuno assegnare l'ora di creazione dello snapshot e le proprietà in quel momento. Si è verificata la modifica a un certo punto tra questi snapshot. Esaminando l'esempio precedente, è possibile osservare che la proprietà modificata sia stata **supportsHttpsTrafficOnly**.

Per confrontare i risultati a livello di codice, confrontare il **contenuto** parte di ogni snapshot per determinare la differenza. Se si confronta l'intero snapshot, il **timestamp** Mostra sempre come una differenza nonostante viene previsto.

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nel [query Starter](../samples/starter.md).
- Vedere avanzata utilizza [query avanzate](../samples/advanced.md).
- Informazioni su come [esplorare risorse](../concepts/explore-resources.md).