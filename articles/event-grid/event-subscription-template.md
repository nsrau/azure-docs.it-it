---
title: Sottoscrizione di griglia di eventi di Azure con modello
description: Creare una sottoscrizione di griglia di eventi con un modello di gestione risorse.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Usare Gestione risorse di modello per la sottoscrizione di eventi griglia

In questo articolo viene illustrato come utilizzare un modello di gestione risorse di Azure per creare sottoscrizioni di griglia di eventi. Il formato da utilizzare varia in base al effettua la sottoscrizione a eventi di gruppo di risorse, o per un determinato tipo di risorsa. Entrambi i formati sono illustrati in questo articolo.

## <a name="subscribe-to-resource-group-events"></a>Sottoscrivere gli eventi di gruppo di risorse

Quando si sottoscrive gli eventi di gruppo di risorse, utilizzare `Microsoft.EventGrid/eventSubscriptions` per il tipo di risorsa. Tipo di punto per l'evento, utilizzare una `WebHook` o `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Quando si distribuisce il modello in un gruppo di risorse, sottoscrivere gli eventi per il gruppo di risorse.

## <a name="subscribe-to-resource-events"></a>Sottoscrivere gli eventi di risorse

Quando si sottoscrive gli eventi di risorse, includendo il tipo di risorsa e il nome nella definizione della sottoscrizione è associare la sottoscrizione della risorsa corretto. Per il tipo di risorsa, utilizzare `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Per il nome, utilizzare `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Tipo di punto per l'evento, utilizzare una `WebHook` o `EventHub`.

Nell'esempio seguente viene illustrato come sottoscrivere gli eventi di archiviazione Blob.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md).
* Per un'introduzione a Gestione risorse, vedere [Panoramica di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md).
* Per iniziare a usare Griglia di eventi, vedere [Creare e instradare eventi personalizzati con Griglia di eventi di Azure](custom-event-quickstart.md).